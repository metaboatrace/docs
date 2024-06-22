# Meta Boatrace

## この組織について

- ボートレース関連のOSS開発を行う組織（一部のリポジトリは非公開）

## リポジトリ一覧

| 名前 | 概要 |
| ---- | ---- |
| [models](https://github.com/metaboatrace/models) | サービス横断で利用するエンティティのPythonパッケージ |
| [scrapers](https://github.com/metaboatrace/scrapers) | ボートレース関連のWebサイトのスクレイピング用Pythonパッケージ |
| [Crawlers](https://github.com/metaboatrace/crawlers) | スクレイピングからデータの永続化までを行うリポジトリ |
| ML（非公開） | 機械学習のためのデータ処理やクレンジング・モデルの生成などを行うリポジトリ |

## 用語定義

### データ

| 用語 | 説明 |
| ---- | ---- |
| **OriginData** | 公式サイトからスクレイピングした原始的なデータ |
| **DataPackageSource** | OriginData をもとに集計したレーサーやレース場の統計データをまとめたもの |
| **DataPackageSourceSuite** | DataPackage　を作成するために必要な DataPackageSource の一式 |
| **DataPackage** | OriginData をベースにレースの全情報を包括的にまとめて、ワンパッケージにしたデータ |
| **PreparedData** | DataPackageを前処理して、モデルの学習や予測に使えるように整えたデータ |

## 運用

- [OriginData の収集](./operations/crawl_origin_data.md)
- [DataPackage の生成](./operations/create_data_package.md)
- [PreparedData の生成](./operations/create_prepared_data.md)
