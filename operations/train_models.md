# モデルの訓練

※ 以下、 [ML](https://github.com/metaboatrace/ml) リポジトリで作業を行うものとする  
※ スクリプト実行時に `PYTHONPATH=.:$PYTHONPATH` が省略されている場合は、 `uv pip install -e .` が実行済みであること

## 前提条件

- `FeatureDataset` を生成済みであること

## 概要

モデルの訓練には、2つのアプローチが存在する

| 手法 | スクリプト | 概要 |
| --- | --- | --- |
| **ホールドアウト検証** | `train_model_holdout.py` | 全データのうち最後の1年をテストデータとして評価する方法 |
| **時系列交差検証** | `train_model_cv.py` | Expanding Window などの手法を用いて、時系列データに適した交差検証を行う方法 |

これらのスクリプトは対話モード（Interactive Mode）をサポートしているため、引数を指定せずに実行すると、学習対象やパラメータを順次選択可能

## 実行例

### ホールドアウト検証による訓練

#### 用途

- 基本的なモデルの性能確認
- 最終的なモデル構築

#### 実行手順

```bash
uv run scripts/train_model_holdout.py
```

引数を指定せずに実行すると、対話形式で以下の設定が可能

| 項目 | 説明 |
| --- | --- |
| **ターゲット選択** | 予測対象（例: `result_rank`） |
| **データ形式** | `vertical` / `horizontal` |
| **データファイルの選択** | 生成済みの Parquet ファイル |
| **モデルタイプ** | `lightgbm`, `xgboost` など |
| **訓練モード** | `basic` (高速) / `full` (SMOTE + GridSearchCV) |
| **閾値最適化指標** | `f1`, `precision_focused` など |

対話モードをスキップして引数で直接指定することも可能

```bash
uv run scripts/train_model_holdout.py \
  --target result_rank \
  --model lightgbm \
  --mode basic \
  --balance-ratio 1.0 \
  --output models/my_model.pkl
```

### 時系列交差検証による訓練

#### 用途

- モデルの汎化性能に対する厳密的な評価

#### 実行手順

```bash
cd ml
uv run scripts/train_model_cv.py
```

引数を指定せずに実行すると、対話形式で以下の設定が可能

| 項目 | 説明 |
| --- | --- |
| **ターゲット選択** | 予測対象（例: `result_rank`） |
| **モデルタイプ** | `lightgbm`, `xgboost` など |
| **訓練モード** | `basic` (高速) / `full` (SMOTE + GridSearchCV) |
| **閾値最適化指標** | `f1`, `precision_focused` など |

対話モードをスキップして引数で直接指定することも可能

```bash
uv run scripts/train_model_cv.py \
  --target result_rank \
  --model xgboost \
  --years 2020 2021 2022 \
  --cv-strategy expanding \
  --output models/cv_result.json \
  --input "outputs/feature_dataset"
```

## 出力

訓練が完了すると、以下の成果物が生成される（スクリプトやオプションによる）

- **モデルファイル (`.pkl`)**: 学習済みモデル
- **メタデータ (`.json`)**: 学習時のパラメータや評価指標
- **評価レポート**: コンソールに出力される精度、F1スコア、重要度などのメトリクス

## 注意事項

- ranker系のモデル（`lightgbm_ranker` など）を使用する場合は、データ形式として `vertical` を選択する必要がある
- `full` モードは計算コストが高いため、大規模なデータセットで使用する場合は時間がかかることがある
