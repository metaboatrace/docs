# 投票シミュレーション（バックテスト）

学習済みモデルを使用して、過去のデータに対する投票シミュレーション（バックテスト）を行う手順について説明します。

## 概要

`ml/scripts/backtest_predictions.py` スクリプトを使用して、以下の検証を行います。

1. **レース選択**: 指定した条件やモデルに基づいて、投票対象とするレースを選定
2. **着順予測**: 学習済みモデルを使用して、各艇の入賞確率を予測
3. **賭け式生成**: 予測確率とオッズに基づいて、期待値の高い買い目を生成
4. **収支計算**: 実際のレース結果と照らし合わせ、的中判定と収支計算を行う

## 前提条件

- `FeatureDataset` が生成済みであること
- 評価に使用する学習済みモデル (`.pkl`) が存在すること
  - `train_model_cv.py` または `train_model_holdout.py` で生成されたもの

## 設定ファイルの作成

シミュレーションを行うには、JSON形式の設定ファイルが必要です。
`ml/configs/prediction/` ディレクトリに配置することを推奨します。

### 設定ファイルの構造

```json
{
  "race_selection": {
    "models": [
      {
        "path": "models/race_selection_model.pkl",
        "threshold": 0.5
      }
    ]
  },
  "racer_selection": {
    "models": [
      {
        "path": "models/lightgbm_over10k_f1pb_20260211.pkl",
        "thresholds": {
          "first": 0.5,  // 未使用（将来用）
          "second": 0.3, // 未使用（将来用）
          "third": 0.2   // 未使用（将来用）
        }
      }
    ]
  },
  "betting": {
    "budget_per_race": 10000,
    "target_return_rate": 1.5,
    "combination_strategy": "uncertainty_aware_kelly",
    "odds_range": {
      "min": 10.0,
      "max": 100.0
    }
  }
}
```

### 主要な設定項目

- **race_selection**: レース参加判定モデル（高配当レース予測など）の設定。`null` の場合は全レース参加。
- **racer_selection**: 着順予測モデルの設定。`path` に学習済みモデルのパスを指定します。
- **betting**: 賭け戦略の設定。
  - `budget_per_race`: 1レースあたりの予算（円）
  - `combination_strategy`: 資金配分戦略 (`box`, `kelly`, `uncertainty_aware_kelly` など)
  - `odds_range`: 狙うオッズの範囲

## 実行方法

### 1. 対話形式で実行

設定ファイルのみを指定して実行すると、検証対象の年（年またぎ不可）を対話形式で選択できます。

```bash
uv run ml/scripts/backtest_predictions.py ml/configs/prediction/my_config.json
```

### 2. 年を指定して実行

`--years` オプションで検証対象の年を指定できます（複数指定可）。

```bash
uv run ml/scripts/backtest_predictions.py ml/configs/prediction/my_config.json --years 2023 2024
```

### 3. 出力先を指定して実行

デフォルトでは `outputs/backtest` に結果が保存されますが、`--output-dir` で変更可能です。

```bash
uv run ml/scripts/backtest_predictions.py ml/configs/prediction/my_config.json --output-dir my_results
```

## 出力結果

実行が完了すると、指定したディレクトリに以下のファイルが生成されます。

- **results_YYYYMMDD_...json**: シミュレーション結果のサマリー（回収率、的中率、収支など）
- **bets_YYYYMMDD_...csv**: 全賭け履歴の詳細データ（レースID、買い目、金額、結果など）
