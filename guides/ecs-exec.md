## ECS Exec

デバッグなどでコンテナにアクセスできると便利なことがある  
今回はサービスとしてECSを利用しているので、ECS Exec を利用可能

### 接続方法

- `brew install --cask session-manager-plugin` を実行し、Session Manager プラグインをインストールしておく
- [infra](https://github.com/metaboatrace/infra)リポジトリのIaCを利用して `terraform apply` を実行し、インフラリソースを生成する
- AWSマネージメントコンソールなどでクラスタ名やタスクのIDなどを確認する
- 以下のコマンドを実行

```bash
$ aws ecs execute-command \
  --cluster <cluster_name> \
  --task <ecs_task_id> \
  --container <container_name> \
  --interactive \
  --command "/bin/sh"
```

### コンテナからデータベースへ接続

ECS Exec での接続を行った後に、以下を実行

```bash
$ apt-get update
$ apt-get install -y postgresql-client
$ psql -h <aurora_cluster_endpoint> -U root -d <database_name>
```
