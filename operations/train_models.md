# モデルの訓練

 `generate_daily_feature_datasets.py` で生成した `FeatureDataset` を使用して、競艇予測モデルを訓練する手順です。
`ml` リポジトリの以下のスクリプトを使用します。

## 概要

モデルの訓練には、主に以下の2つのアプローチがあります。

1.  **ホールドアウト検証 (`train_model_holdout.py`)**: 全データのうち最後の1年をテストデータとして評価する方法。
2.  **時系列交差検証 (`train_model_cv.py`)**: Expanding Window などの手法を用いて、時系列データに適した交差検証を行う方法。

これらのスクリプトは対話モード（Interactive Mode）をサポートしており、引数を指定せずに実行すると、学習対象やパラメータを順次選択できます。

## 前提条件

- `ml` リポジトリのセットアップと、`FeatureDataset` の生成が完了していること。

## 1. ホールドアウト検証による訓練

基本的なモデルの性能確認や、最終的なモデル構築に使用します。

### 実行手順

```bash
cd ml
uv run scripts/train_model_holdout.py
```

コマンドを実行すると、対話形式で以下の設定を求められます。

- **ターゲット選択**: 予測対象（例: `result_rank`）
- **データ形式**: Vertical / Horizontal
- **データファイルの選択**: 生成済みの Parquet ファイル
- **モデルタイプ**: `lightgbm`, `xgboost` など
- **訓練モード**: `basic` (高速) / `full` (SMOTE + GridSearchCV)
- **閾値最適化指標**: `f1`, `precision_focused` など

### コマンドライン引数による実行

対話モードをスキップして引数で直接指定することも可能です。

```bash
uv run scripts/train_model_holdout.py \
  --target result_rank \
  --model lightgbm \
  --mode basic \
  --balance-ratio 1.0 \
  --output models/my_model.pkl
```

## 2. 時系列交差検証による訓練

モデルの汎化性能をより厳密に評価するために使用します。

### 実行手順

```bash
cd ml
uv run scripts/train_model_cv.py
```

### コマンドライン引数による実行例

```bash
uv run scripts/train_model_cv.py \
  --target result_rank \
  --model xgboost \
  --years 2020 2021 2022 \
  --cv-strategy expanding \
  --output models/cv_result.json
```

## 出力

訓練が完了すると、以下の成果物が生成されます（スクリプトやオプションによる）。

- **モデルファイル (`.pkl`)**: 学習済みモデル
- **メタデータ (`.json`)**: 学習時のパラメータや評価指標
- **評価レポート**: コンソールに出力される精度、F1スコア、重要度などのメトリクス

## 注意事項

- ranker系のモデル（`lightgbm_ranker` など）を使用する場合は、データ形式として `vertical` を選択する必要があります。
- `full` モードは計算コストが高いため、大規模なデータセットで使用する場合は時間がかかることがあります。
