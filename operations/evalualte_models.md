## モデルの評価

### 前提条件

- 汎化性能を確認可能な DataPackage (時系列的にモデルの訓練に利用したデータより先のもの) が用意できていること
- 訓練済みモデルがすでに生成されていること
- 予想設定ファイル一式が準備できていること

### 予想設定ファイル一式

モデルの推論結果とは別にルールベースで除外したい条件や、投票時の掛け金などの設定をJSON形式で保持するファイルである。  
以下のようなファイル名で一つのディレクトリにまとめる。

```bash
$ ls -l data/simulation/suite/high_payout_2406221342
.rw-r--r--  140 k0kishima 22 6  13:44 bet_setting.json
.rw-r--r-- 1.3k k0kishima 22 6  13:59 placement_conditions.json
.rw-r--r--   74 k0kishima 22 6  13:44 race_selection_conditions.json
```

### シミュレーションの実行

以下のように、 DataPackage、モデル本体、設定ファイルが配置されているディレクトリをそれぞれ引数としてスクリプトを実行する。

```bash
$ python scripts/simulation.py data/tmp/packages/2022_2.csv metaboatrace/ml/models/high_payout/prod/20240622_132333/model.pkl data/simulation/suite/high_payout_2406221342
Simulating races: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 27161/27161 [25:09<00:00, 18.00it/s]
Total Investment: ¥12,723,900
Total Return: ¥14,649,920
Net Profit: ¥1,926,020
ROI: 115.14%
Hit Rate: 9.34% (245 hits out of 2624 races)
Simulation results and log saved in data/tmp/simulation/20240622_145707
```
