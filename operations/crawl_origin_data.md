## 公式サイトをクロール

[Crawlers](https://github.com/metaboatrace/crawlers) リポジトリにて以下のスクリプトを実行する。

```bash
$ python scripts/crawl_data_for_period.py 2022-05-01 2022-05-10
```

これで、指定した期間のすべてのデータを収集できる。

## データの管理

### エクスポート

#### レースデータの管理

収拾したデータは、レーサーの級別審査期間単位で管理する。  
Crawler リポジトリにて、以下のスクリプトを実行すると対象の期以外のデータを削除した上でアーカイブファイルが出力される。

```bash
$ python scripts/trim_data_for_specified_term.py 2021 1
```

※ データ削除前にバックアップが自動的に生成される

#### レーサーデータの管理

前項のアーカイブファイルには、 `racers` と `stadiums` は含まれていない。  
前者に関しては手動で別途管理する（後者に関してはマスターデータをスクリプト経由でいつでも入れられるので、ここではケアしない）。

以下のように `racers` のみエクスポート対象とできる。

```bash
$ pg_dump -h 127.0.0.1 -p 55432 -U postgres -d metaboatrace_development -n public --data-only --table=racers -f racers_$(date +"%Y%m%d%H%M%S").dump
```

### インポート

前節で作成したアーカイブファイルを、以下のようにインポートできるので適宜実施する。

```bash
$ psql -h 127.0.0.1 -p 55432 -U postgres -d metaboatrace_development -f 20200501.dump
```

## 欠損データの補完

一部のデータの欠損は、スクリプトで補完が可能である。

### レーサーの登録期

公式サイトでは、レーサーが引退すると、プロフィールは閲覧できなくなる。  
その場合、特徴量として利用する重要なデータが取得することができない。

レーサーのデータのうち、登録期に関しては、以下のスクリプトを実行することで登録番号から推測・更新することができる。

```bash
$ python scripts/update_incomplete_racer_terms.py
```
