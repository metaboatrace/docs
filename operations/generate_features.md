# FeatureDataset の生成

※ 以下、 [ML](https://github.com/metaboatrace/ml) リポジトリで作業を行うものとする  
※ スクリプト実行時に `PYTHONPATH=.:$PYTHONPATH` が省略されている場合は、 `uv pip install -e .` が実行済みであること

## 前提条件

- 対象期間および、その集計に必要な過去データが `OriginData` としてデータベースに存在すること
  - **レーサー統計**: 対象日の級別審査期間と、それ以前の過去3年分 (`racer_aggregation_years=3`)
  - **競艇場統計**: 対象日以前の全データ (`stadium_aggregation_years=99`)
  - ※ 集計期間の設定は `ml/metaboatrace/ml/features/builder/config.py` で定義されていて、今のところ固定値

## 実行手順

```bash
uv run scripts/generate_daily_feature_datasets.py --start-date YYYY-MM-DD --end-date YYYY-MM-DD
```

### オプション

| オプション | 説明 | デフォルト |
| --- | --- | --- |
| `--start-date` | [必須] 生成開始日 (YYYY-MM-DD) | - |
| `--end-date` | [必須] 生成終了日 (YYYY-MM-DD) | - |
| `--output-dir` | 出力先ディレクトリ | `outputs/feature_dataset` |
| `--log-mode` | ログ出力モード (`tqdm` または `verbose`) | `tqdm` |
| `--overwrite` | 既存のファイルを上書きする場合に指定 | False |
| `--dry-run` | ファイル保存を行わず、処理のみをシミュレーション | False |

### 実行例

2021年1月1日から2021年12月31日までのデータを生成する場合:

```bash
uv run scripts/generate_daily_feature_datasets.py \
  --start-date 2021-01-01 \
  --end-date 2021-12-31 \
  --overwrite
```

## 出力

デフォルトでは `outputs/feature_dataset` 配下に以下の構成で出力される。

- `vertical/YYYY/YYYY-MM-DD.parquet`: レーサー単位（Vertical形式）のデータセット
- `horizontal/YYYY/YYYY-MM-DD.parquet`: レース単位（Horizontal形式）のデータセット

生成されたファイルは、モデル訓練スクリプトの入力として使用される。
