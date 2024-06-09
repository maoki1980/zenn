---
title: "Google Colab でSQLクエリを実行する"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["googlecolaboratory", "database", "postgresql", "python"]
published: true
---

## 要約

Google Colaboratory (Colab) でSQLクエリを実行できる。

## PostgreSQLサーバーのDBに接続する

ここでは、既にPostgreSQLサーバーがあるものとして進める。以下のように拡張機能を読み込んだ後、データベースに接続を行う。PostgreSQLサーバーのDB接続情報は以下とする。

- ホスト名 (またはIPアドレス): `hogehoge.com`
- ポート番号: `6320`
- DBユーザー名: `postgres`
- DBパスワード: `password`
- 接続データベース名: `postgres`

```python
# SQL拡張機能をロードする
%reload_ext sql
# PostgreSQLサーバーのDB (postgres) に接続する
%sql postgresql://postgres:password@hogehoge.com:6320/postgres
```

## SQLクエリの実行

マジックコマンド`%%sql`を先頭に書くことで、そのセルでSQLを実行できる。

例えば、`postgres`データベースのテーブル一覧を取得するSQLクエリを実行してみる。

```python
# SQLクエリを実行する
%%sql
SELECT
    table_catalog
  , table_schema
  , table_name
FROM
  information_schema.tables
WHERE
  table_type = 'BASE TABLE'
LIMIT
  10
```

SQLクエリの実行結果はセルの出力として表示される。また変数`_`に自動的に格納される。この`_`は直前に実行されたマジックコマンドの結果を得るための特殊な変数となっている。

```python
# SQLクエリの実行結果 (変数_に入る) を出力する
print(_)
```

とすれば取得した結果を表示できる。

## SQLクエリの実行結果をPandasデータフレームに変換する

マジックコマンドの`%%sql`を`%%sql 変数名 <<`とすると、`変数名`という名前の変数にSQLクエリの実行結果を格納できる。

```python
# SQLクエリを実行し、結果を変数resultに格納する
%%sql result <<
SELECT
    table_catalog
  , table_schema
  , table_name
FROM
  information_schema.tables
WHERE
  table_type = 'BASE TABLE'
```

次にpandasデータフレームに変換する。

```python
# Pandasデータフレームに変換する
df = result.DataFrame()
display(df)
```

## 参考コード

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](http://colab.research.google.com/github/maoki1980/zenn/blob/main/notebooks/google-colab-sql/01_google-colab-sql.ipynb)

## 関連記事

- [【2023年版】Google ColabでSQLを使う【DuckDB, JupySQL】 #Python - Qiita](https://qiita.com/_jinta/items/479355d4709cd30d56c8)
