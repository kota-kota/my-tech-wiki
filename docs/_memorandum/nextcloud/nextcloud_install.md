# NextCloudをインストールする

オープンソースのオンラインストレージであるNextCloudをインストールする手順を記載します。

## Dockerイメージを利用してUbuntuへNextCloudをインストールする

Docker Hubに、NextCloudが既に導入済みのイメージがあるため、それを利用して導入します。

### 導入環境について

導入した環境は以下のとおりです。個人サーバとして利用しているシングルボードを使用します。

- サーバPC：ROCK64 single-board computer (ARM64)
- OS：Ubuntu 18.04.1 LTS (bionic)
- IPアドレス：192.168.0.10

Dockerイメージは以下を使用します。サーバPCがarm64アーキテクチャなので、arm64に対応している必要があります。

- Docker Hub - nextcloud  
  <https://hub.docker.com/_/nextcloud>
- Docker Hub - arm64v8/nextcloud  
  <https://hub.docker.com/r/arm64v8/nextcloud>

きちんとした環境構築を実施する前に、試しに最新フルパッケージを導入して動かしてみました。

`arm64v8/nextcloud` のイメージを `docker run` します。ポート番号はひとまず8080を指定しました。

```bash
$ docker run -d -p 8080:80 arm64v8/nextcloud
```

このコマンドだけで、イメージのダウンロード・コンテナ生成・コンテナ実行まで実施されます。

<http://192.168.0.10:8080/> にアクセスしてみます。NextCloudの初期設定画面が表示されれば成功です。

### 事前準備

`docker-compose.yml` を配置するディレクトリ `/app/nextcloud` を作成します。

```bash
$ mkdir -p /app/nextcloud                         # 1
$ mkdir /app/nextcloud/html                       # 2
$ mkdir /app/nextcloud/sb                         # 3
$ chown www-data:www-data /app/nextcloud/html     # 4
```

1. `docker-compose.yml` を配置するディレクトリ `/app/nextcloud` を作成します。
1. NextCloudがホスト側で保持しておくデータを格納するディレクトリ `/app/nextcloud/html/` を作成します。
1. MySQLがホスト側で保持しておくデータを格納するディレクトリ `/app/nextcloud/db/` を作成します。
1. パーミッションをApacheユーザである `www-data` に変更します。

### docker-compose.ymlを作成する

以下のような内容で、`/app/nextcloud/docker-compose.yml` を生成します。

```yaml
version: "2.1"
services:
  db:
    image: mariadb
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
    - /app/nextcloud/db:/var/lib/mysql
    environment:
    - MYSQL_ROOT_PASSWORD=(pass)
    - MYSQL_PASSWORD=(pass)
    - MYSQL_DATABASE=nextcloud
    - MYSQL_USER=nextcloud

  app:
    image: arm64v8/nextcloud
    restart: always
    container_name: nextcloud
    volumes:
    - /app/nextcloud/html:/var/www/html
    ports:
    - 8080:80
    links:
    - db
    environment:
    - MYSQL_PASSWORD=(pass)
    - MYSQL_DATABASE=nextcloud
    - MYSQL_USER=nextcloud
    - MYSQL_HOST=db
```

### NextCloudのコンテナを起動する

`docker-compose` コマンドを実行し、コンテナを作成し実行します。

```bash
$ docker-compose up -d
```

Apache用の `8080/tcp` と MariaDB用の `3306/tcp` のアクセスを許可します。

<http://サーバーアドレス:ポート番号> にアクセスし、NextCoudページが表示されることを確認します。

ユーザ登録したらダッシュボードが表示されるはずです。
