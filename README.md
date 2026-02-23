# Meta Boatrace

## この組織について

- ボートレース関連のOSS開発を行う組織（一部のリポジトリは非公開）

## リポジトリ一覧

| 名前 | 概要 |
| ---- | ---- |
| [models](https://github.com/metaboatrace/models) | サービス横断で利用するエンティティのPythonパッケージ |
| [scrapers](https://github.com/metaboatrace/scrapers) | ボートレース関連のWebサイトのスクレイピング用Pythonパッケージ |
| [Crawlers](https://github.com/metaboatrace/crawlers) | スクレイピングからデータの永続化までを行うサービス (OriginData を生成) |
| ML（非公開） | 機械学習のためのデータクレンジングからモデルの学習や評価までを行うサービス |
| [infra](https://github.com/metaboatrace/infra) | インフラリソースを一元的に IaC で管理するためのリポジトリ |
| [engineering-standards](https://github.com/metaboatrace/engineering-standards) | 組織全体のエンジニアリング標準と共通設定ファイルを管理するリポジトリ |

※ サービスは頭文字を大文字で表記  
※ 各リポジトリのREADMEにはローカル開発環境の構築方法を記載し、それ以上の詳細はこのリポジトリで管理  
※ 開発標準は [engineering-standards](https://github.com/metaboatrace/engineering-standards) に集約し、このリポジトリではビジネスの次元(ブラックボックス視点)での文書化のみ行う

## 用語定義

### データ

| 用語 | 説明 |
| ---- | ---- |
| **OriginData** | 公式サイトなどからスクレイピングした原始的なデータ |
| **AggregatedData** | OriginData をもとに生成される中間データ (統計情報など) |
| **FeatureDataset** | モデルの学習や推論に使用するために、OriginData を加工・整形したデータ |

## 運用

### データセットの生成

- [OriginData の収集](./operations/crawl_origin_data.md)
- [FeatureDataset の生成](./operations/generate_features.md)

### モデルの訓練と評価

- [モデルの訓練](./operations/train_models.md)

### シミュレーション

- [投票シミュレーション（バックテスト）](./operations/simulate_betting.md)
