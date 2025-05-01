## DataPackage の生成

※ 以下、 MLリポジトリで作業を行うものとする  
※ コマンド実行前に `source .venv/bin/activate` などで仮想環境のアクティーベーションを済ませてあるものとする

### DataPackage の管理ポリシー

DataPackage は全モデルから利用されるものであるため、**下位互換性を常に保つものとする**  

### DataPackage の生成手順

#### 前提条件

- データ生成対象の期に対応する AggregatedData が用意できていること

#### DataPackageSource の生成

AggregatedData を `DataPackage` の生成に用いる。  
ただし、AggregatedData は期単位でファイルが分かれているため、 `DataPackage` の生成に用いる際にはファイルをまとめておく必要がある。

以下のようにスクリプトを実行すれば、ファイルをまとめることができる。

```bash
$ PYTHONPATH=.:$PYTHONPATH python scripts/create_data_package_sources.py --year 2021 --period_type 2 --terms_count_stadium 3 --terms_count_racer 2
Stadium terms to be merged:
2021_1
2020_2
2020_1

Racer terms to be merged:
2021_1
2020_2

Do you want to proceed with these terms? (yes/no): yes
```

まとめられたファイルは以下のように配置される。

```
data/suite/{year}/{period_type}/
  racer_winning_trick.csv
  racer_st.csv
  racer_elative_performance_metrics.csv
  racer_placement.csv
  stadium_winning_trick.csv
```

これらは、 `DataPackage` の生成に利用されるため、 `DataPackageSource` と名付ける。

#### 生成スクリプトの実行

前節で生成した `DataPackageSource` のパスは、 `scripts/generate_race_data_package.py` にて規約で識別されるようになっている。

規約に則ったパスに　`DataPackageSource` が配置できているなら、以下のように期間を指定して　`DataPackage` を作成できる。

```bash
$ PYTHONPATH=.:$PYTHONPATH python scripts/generate_race_data_package.py 2021-11-01 2021-11-30
```

リーク防止のため、これより時系列的に後のデータを含む AggregatedData を使用しないように注意すること[^1]。　　
`data/suite/{year}/{period_type}/` に `DataPackageSource` を作成した場合は、その直下にある `merge_log.txt` というファイルにどの AggregatedData をマージしたかが記録されている。

日別に生成されたCSVは、以下のユーティリティスクリプトの実行により期別に統合が行える。

```bash
$ PYTHONPATH=.:$PYTHONPATH python scripts/combine_data_packages.py 2021 2                    
Combined data saved to data/tmp/packages/2021_2.csv
```

[^1]: 例えば、2023年後期の期初から1ヶ月分のデータを作るなら `python scripts/generate_race_data_package.py 2023-11-01 2023-11-30` のようなコマンドになるが、算入する AggregatedData に2023年後期以降のデータを含んでいたらリークになる
