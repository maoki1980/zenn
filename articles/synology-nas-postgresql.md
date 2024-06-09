---
title: "Synology NAS でDockerを用いてPostgreSQLサーバーを立てる"
emoji: "🎉"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["docker", "postgresql", "nas", "database"]
published: true
---

## 要約

DiskStation DS1823xs+ というSynologyのNASを使っているがPostgreSQLサーバーの機能はない。そこで Container Manager の機能を使ってDockerコンテナとしてPostgreSQLサーバーを構築する。

## Synology NAS に必要なパッケージをインストールする

SynologyのNAS、DiskStation DS1823xs+ を使っているが、PostgreSQLサーバーのパッケージはなさそう。そこで、PostgreSQLのDockerコンテナを用いてサーバを立てる。

まず、NASのDSMからパッケージセンターを開き、Container Manager のインストール状態を確認し、インストールされていない場合はインストールする。

![Container Manager](/images/synology-nas-postgresql/2024-06-09_124448.png)

## Container Manager でDockerイメージを取得する

Container Manager を開き、レジストリからpostgresqlのものを探す。

![レジストリ一覧](/images/synology-nas-postgresql/2024-06-09_125427.png)

ここではpostgresを選択し、postgresのDockerイメージをダウンロードする。今回PostgreSQLのバージョンは11.11のものが必要であったため、タグから`11.11-alpine`を選択し、適用ボタンを押す。

ダウンロードが始まり、イメージのところにpostgresのイメージが現れることを確認する。

![イメージ一覧](/images/synology-nas-postgresql/2024-06-09_130253.png)

## PostgreSQLのDockerコンテナを起動する

イメージの`postgres`を選択し、実行ボタンを押す。全般設定として、任意の重複しないコンテナ名を入力し、自動再起動を有効にしておく。

![全般設定](/images/synology-nas-postgresql/2024-06-09_130730.png)

次に詳細設定を行う。ポート設定としてフォワーディングの設定をする。今回はNASにポート番号`6320`でアクセスした場合に、コンテナのPostgreSQLのポートに転送されるようにマッピング設定をしておく。ボリューム設定としてNASの任意のディレクトリをPostgreSQLのデータディレクトリ (`/var/lib/postgresql/data`) としてマウントする設定を行う。ここではNASの`/docker/postgres`というディレクトリをマウントする。

![詳細設定のポート設定およびボリューム設定](/images/synology-nas-postgresql/2024-06-09_131523.png)

環境変数の設定も行う必要がある。

- `LANG`
    `ja_JP.utf8`に変更
- `TZ`
    `Asia/Tokyo`に指定
- `POSTGRES_PASSWORD`
    postgresユーザーのパスワードを設定

そのほかの環境変数については以下のドキュメントを参考にして必要に応じて設定する。

- [postgres - Official Image | Docker Hub](https://registry.hub.docker.com/_/postgres/)

![環境変数設定](/images/synology-nas-postgresql/2024-06-09_132359.png)

「機能」「ネットワーク」「実行コマンド」「リンク」については今回はデフォルトのままで進める。

要約画面で設定内容を確認し、「ウィザード終了後、このコンテナを実行」にチェックが入っていることを確認し、完了ボタンを押す。

Container Managerのコンテナ欄に作成したコンテナ名が現れて、状態がグリーンならPostgreSQLサーバーとして起動している。

![コンテナ一覧](/images/synology-nas-postgresql/2024-06-09_133403.png)

PostgreSQLクライアントから接続できることを確認して作業完了。

## 関連記事

- [Synology NAS上でPostgreSQLをDockerコンテナとして動かす](https://zenn.dev/yuyakato/articles/3059fb69da41cd)
