## PreparedData の生成手順

※ 以下、 MLリポジトリで作業を行うものとする  
※ コマンド実行前に `source .venv/bin/activate` などで仮想環境のアクティーベーションを済ませてあるものとする

### 前提条件

- 対象の期の DataPackage が存在すること

### DataPackage の統合

`PreparedData` は DataPackage をベースに作られるが、DataPackageは生成直後日別に生成されているので、このスクリプトで処理しやすいようにまとめる必要がある。  
DataPackage を期ごとにまとめるには、以下のようにスクリプトを実行する。

```bash
$ python scripts/combine_data_packages.py 2021 1
```

### CSVの生成

以下のようにスクリプトを実行すればインタラクティブにどの期の DataPackage をもとにするかや、どのモデルを対象に前処理をするかを選択できる。

```bash
$ PYTHONPATH=.:$PYTHONPATH python scripts/preparing.py
[?] Select file(s) to preprocess: 
   [X] 2021_2.csv
 > [X] 2022_1.csv

Selected file(s):
2021_2.csv
2022_1.csv
[?] Select a preprocessing strategy: 
 > high_payout
   placement

Selected strategy: high_payout
Processing races: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 45499/45499 [11:38<00:00, 65.15it/s]
Prepared data and log saved in data/prepared/20240622_084705
```

生成されたデータ( `PreparedData` )はモデルの訓練でそのまま使える。  
Vertical DataFrame / Horizontal DataFrame もここで分かれる。
