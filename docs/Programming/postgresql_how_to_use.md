---
title: PostgreSQLを使ってみた
---

## データベース操作

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

## ロール操作

パスワードを変更する。

```text
postgres=# ALTER ROLE xxxx with password '??????';
```
