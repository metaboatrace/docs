## DataPackage の管理ポリシー

※ 以下、 MLリポジトリで作業を行うものとする

**DataPackage は下位互換性を常に保つものとする**  
※ 全モデルから利用されるものであるため

## DataPackage の生成手順

### 期別集計データの準備

以下の集計データを事前に準備しておく。

- ボートレース場の気象情報別決まり手発生確率
- レーサーのコース別決まり手成功確率
- レーサーのコース別スリット相対指標
  - ※ 1コース差され率・まくられ率、2コース逃し率のこと
- レーサーのコース別スタート成績
- レーサーのコース別着順成績

これらはSQLをベースに導出することが可能だが、都度導出するオーバーヘッドが発生すると DataPackage 生成に時間がかかってしまう。  
そこで、予め集計をしておいてキャッシュとして用いることで、 DataPackage の再生成が必要になった際の時短とする。  
これが集計データを事前に準備しておく理由である。

管理の便宜上、集計ファイルはCSVとして保存し、レーサーの級別審査期間単位で分けておく。  
DataPackage を生成する際には複数の期を横断した集計結果が必要になるが、その場合は集計ファイルをマージしたものを用いる。  
これに関しては後述する。

#### ボートレース場に関するデータの集計

すでに集計対象の機関において OriginData が収集済みであることが前提である。  
（後続のレーサーの項でも同様とする）

以下のように、起点とする期から過去何期分集計するかを引数で指定して実行する。

```bash
$ python scripts/aggregate_stadium_data_by_period.py 2022 2 6
```

上記では、以下のように6期分CSVファイルが生成される。

```
$ ls -l data/aggregation/stadium/
.rw-r--r--@ 18k k0kishima 11 5  09:26 2020_1.csv
.rw-r--r--  20k k0kishima 11 5  09:26 2020_2.csv
.rw-r--r--  18k k0kishima 11 5  09:26 2021_1.csv
.rw-r--r--  19k k0kishima 11 5  09:26 2021_2.csv
.rw-r--r--  123 k0kishima 11 5  09:26 2022_1.csv
.rw-r--r--  123 k0kishima 11 5  09:26 2022_2.csv
```

#### レーサーに関するデータの集計

レーサーの場合も基本的には前項と同様だが、集計対象のデータ種別が4種あるため、第4引数でデータ種別を指定する必要がある。

```
$ python scripts/aggregate_racer_data_by_period.py 2020 2 1 placement
```

上記は着順データの集計を行う例だが、"all" を指定することで全種同時に集計することが可能である。

本節で生成したCSVは、以降「集計済みCSV」と表記する。

### DataPackageSource の生成

集計済みCSVを `DataPackage` の生成に用いる。  
ただし、集計済みCSVは期単位でファイルが分かれているため、 `DataPackage` の生成に用いる際にはファイルをまとめておく必要がある。

以下のようにスクリプトを実行すれば、ファイルをまとめることができる。

```bash
$ python scripts/create_data_package_sources.py --year 2021 --period_type 2 --terms_count_stadium 3 --terms_count_racer 2
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

### 生成スクリプトの実行

前節で生成した `DataPackageSource` のパスは、 `scripts/generate_race_data_package.py` にて規約で識別されるようになっている。

規約に則ったパスに　`DataPackageSource` が配置できているなら、以下のように期間を指定して　`DataPackage` を作成できる。

```bash
$ python scripts/generate_race_data_package.py 2021-11-01 2021-11-30
```

リーク防止のため、これより時系列的に前のデータを含む集計済みCSVを使用しないように注意すること。　　
`data/suite/{year}/{period_type}/` に `DataPackageSource` を作成した場合は、その直下にある `merge_log.txt` というファイルにどの集計済みCSVをマージしたかが記録されている。

日別に生成されたCSVは、以下のユーティリティスクリプトの実行により期別に統合が行える。

```bash
$ python scripts/combine_data_packages.py 2021 2                    
Combined data saved to data/tmp/packages/2021_2.csv
```
