# Meta Boatrace

## この組織について

- ボートレース関連のOSS開発を行う組織（一部のリポジトリは非公開）

## リポジトリ一覧

| 名前 | 概要 |
| ---- | ---- |
| [models](https://github.com/metaboatrace/models) | サービス横断で利用するエンティティのPythonパッケージ |
| [scrapers](https://github.com/metaboatrace/scrapers) | ボートレース関連のWebサイトのスクレイピング用Pythonパッケージ |
| [Crawlers](https://github.com/metaboatrace/crawlers) | スクレイピングからデータの永続化までを行うサービス (OriginData を生成) |
| [Aggregator](https://github.com/metaboatrace/aggregator) | 集計を行うサービス (AggregatedData を生成) |
| ML（非公開） | 機械学習のためのデータクレンジングからモデルの学習や評価までを行うサービス |
| [infra](https://github.com/metaboatrace/infra) | インフラリソースを一元的に IaC で管理するためのリポジトリ |

※ サービスは頭文字を大文字で表記  
※ 各リポジトリのREADMEにはローカル開発環境の構築方法を記載し、それ以上の詳細はこのリポジトリで管理

## 用語定義

### データ

| 用語 | 説明 |
| ---- | ---- |
| **OriginData** | 公式サイトなどからスクレイピングした原始的なデータ |
| **AggregatedData** | OriginData をもとに作成されるレーサーやレース場の集計データ |
| **DataPackageSource** | 指定した期間分 AggregatedData をまとめたもの |
| **DataPackageSourceSuite** | DataPackage　を作成するために必要な DataPackageSource の一式 |
| **DataPackage** | OriginData をベースにレースの全情報を包括的にまとめて、ワンパッケージにしたデータ |
| **PreparedData** | DataPackageを前処理して、モデルの学習や予測に使えるように整えたデータ |

## 運用

### データセットの生成

- [OriginData の収集](./operations/crawl_origin_data.md)
- [AggregatedData の生成](./operations/create_aggregated_data.md)
- [DataPackage の生成](./operations/create_data_package.md)
- [PreparedData の生成](./operations/create_prepared_data.md)

### モデルの訓練と評価

- [モデルの訓練](./operations/train_models.md)
- [モデルの評価](./operations/evalualte_models.md)

## Tips

- [ECSコンテナにアクセスする](./guides/ecs-exec.md)
