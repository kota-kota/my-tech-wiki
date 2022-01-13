---
title: WindowsにPostgreSQLをインストールする
---

## インストールする

PostgreSQL Database Download  
<https://www.enterprisedb.com/downloads/postgres-postgresql-downloads>

Windows x86-64 の バージョン 14.1 をインストールします。

- インストールディレクトリ：C:\Program Files\PostgreSQL\14
- インストールするコンポーネントを選択：全て
- データベース作成先のフォルダ：C:\Program Files\PostgreSQL\14\data
- スーパーユーザ `postgres` のパスワードを指定：postgres
- クライアントからの接続を受けつけるポート番号：5432
- デフォルトのロケールを選択：`C` を選択（ロケールを使わない設定）

インストーラの終了後に Stack Builder を起動するか聞かれるので、チェックを外して「Finish」をクリックします。

## サービスの起動と停止

「ファイル名を指定して実行」で `services.msc` と入力し、サービス管理コンソールを起動します。

サービス一覧から PostgreSQL サービス（サービス名は `postgresql-x64-14` ）を選択し、右クリックして表示されるメニューからサービスの起動や停止を行います。

## データベースへの接続確認

スタートメニューの PostgreSQL フォルダ内の「SQL Shell (SQL)」をクリックし、psql を起動します。

プロンプトが順番に表示されます。
角カッコ内がデフォルト値を表しています。

- 接続先のホスト名 `Server [localhost]`：デフォルトのまま
- データベース名 `Database [postgres]`：デフォルトのまま
- ポート番号 `Port [5432]`：デフォルトのまま
- ユーザ名 `Username [postgres]`：デフォルトのまま
- クライアント側のエンコーディング `Client Encoding [SJIS]`：デフォルトのまま
- パスワード：インストール時に指定したもの

データベースに正常に接続できると、psql のプロンプト `postgres=#` が表示されます。

## サンプル

### データベース操作

データベース一覧を表示する。

```text
postgres=# \l
```

データベースを作成する。

```text
postgres=# CREATE DATABASE mydb;
CREATE DATABASE
```

指定のデータベースに接続する。

```text
postgres=# \c mydb
データベース"mydb"にユーザー"postgres"として接続しました。
```

データベースにテーブルを作成する。
idとnameをもつmytableを作成してみる。

```text
mydb=# CREATE TABLE mytable (
mydb(#  id integer,
mydb(#  name varchar(10)
mydb(# );
CREATE TABLE
```

テーブルの一覧を表示する

```text
mydb=# \dt
             リレーション一覧
 スキーマ |  名前   |  タイプ  |  所有者
----------+---------+----------+----------
 public   | mytable | テーブル | postgres
(1 行)
```

テーブルにデータを追加してみる。

```text
mydb=# INSERT INTO mytable VALUES (526, 'Sasaki');
INSERT 0 1
```

テーブルの中身を表示してみる。

```text
mydb=# SELECT * FROM mytable;
 id  |  name
-----+--------
 526 | Sasaki
(1 行)
```

テーブルを削除する。

```text
mydb=# DROP TABLE mytable;
```

### ロール操作

パスワードを変更する。

```text
postgres=# ALTER ROLE xxxx with password '??????';
```
