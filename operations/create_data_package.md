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
data/data_package_sources/{year}/{period_type}/
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
`data/data_package_sources/{year}/{period_type}/` に `DataPackageSource` を作成した場合は、その直下にある `merge_log.txt` というファイルにどの AggregatedData をマージしたかが記録されている。

## DataPackage の特徴

- 完全に「大は小を兼ねる」という性質を持つ包括的なデータセット
  - これをベースにモデルに応じた前処理をしたり、目的変数を追加したりして訓練データを作成する
- 複数の期を横断したデータ集計が必要
  - AggregatedData は期毎に集計期間が完全に独立しているのに対し、 `DataPackage` は学習に必要なボリュームを担保するため、極力過去のデータを多く利用するようになっている
-  データリーク要注意
  - あるレースを対象とした DataPackage を作るときに利用できるデータは AggregatedData はレースの日付を基準に前期から n期 でなければならない
  - 例えば、2024年4月30日は2024年後期であるため、2024年前期以前のデータしか使ってはいけない
  - 今期のデータも含めてはいけないことに注意
    - なぜなら、本番運用する際にそのレースの期の統計情報は時系列的に存在しないため

[^1]: 例えば、2023年後期の期初から1ヶ月分のデータを作るなら `python scripts/generate_race_data_package.py 2023-11-01 2023-11-30` のようなコマンドになるが、算入する AggregatedData に2023年後期以降のデータを含んでいたらリークになる
