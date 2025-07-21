## AggregatedData の生成

※ 以下、 MLリポジトリで作業を行うものとする  
※ コマンド実行前に `source .venv/bin/activate` などで仮想環境のアクティーベーションを済ませてあるものとする

## AggregatedData の概要

AggregatedData は以下のデータを期別集計したものである

- ボートレース場
  - 気象情報別決まり手発生確率
- レーサー
  - コース別決まり手成功確率
  - コース別スリット相対指標
    - ※ 1コース差され率・まくられ率、2コース逃し率のこと
  - コース別スタート成績
  - コース別着順成績

これらはSQLをベースに導出することが可能だが、都度導出するとオーバーヘッドとなり DataPackage 生成などの処理効率が落ちる。
そこで、予め集計したものをキャッシュのような形で用いることで、 DataPackage の再生成が必要になった際の時短とする。  
これが AggregatedData を事前に準備しておく理由である。

### ボートレース場に関するデータの集計

すでに集計対象の機関において OriginData が収集済みであることが前提である。  
（後続のレーサーの項でも同様とする）


```bash
$ PYTHONPATH=.:$PYTHONPATH python scripts/aggregate_stadium_data_by_period.py --year 2022 --period 2 --terms 6
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

⚠️ 指定した2022年の後期から6期分先ではなく、過去6期分が集計されるので注意

### レーサーに関するデータの集計

レーサーの場合も基本的には前項と同様だが、集計対象のデータ種別が4種あるため、第4引数でデータ種別を指定する必要がある。

```
$ PYTHONPATH=.:$PYTHONPATH python scripts/aggregate_racer_data_by_period.py --year 2020 --period 2 --periods 1 --type all
```

type に "all" を指定することで全種同時に集計することが可能である。
