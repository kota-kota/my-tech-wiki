# NextCloudのDockerイメージでsmbclientを使用する

## docker-composeでsmbclientをインストールできるようにする

NextCloudの公式Dockerイメージは、smbclientがインストールされていないため、Dockerfileとdocker-composeを使ってビルドします。

`/app/nextcloud/` に、以下の内容を記載した `Dockerfile` を置きます。

```text
FROM arm64v8/nextcloud
RUN apt-get update && apt-get install -y libsmbclient-dev smbclient && pecl install smbclient && docker-php-ext-enable smbclient
```

`docker-compose.yml` は以下のように変更します。

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
      - MYSQL_ROOT_PASSWORD=epi-898989
      - MYSQL_PASSWORD=epi-898989
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    build: ./
    restart: always
    container_name: nextcloud
    volumes:
      - /app/nextcloud/html:/var/www/html
    ports:
      - 8080:80
    links:
      - db
    environment:
      - MYSQL_PASSWORD=epi-898989
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db
```

`docker-compose` コマンドを実行します。

```bash
$ docker-compose up -d
```

> **注意**
>
> `Dockerfile` からイメージをビルドする場合、`Dockerfile` の存在するディレクトリの中身はtarで固められdaemonへと送られます。
> そのため、サイズが大きなファイル（NextCloudでいうと `db/` や `html/` のようなディレクトリ）があると、daemonへの送信に時間がかかり、ビルドが進まなくなります。
>
> 以下のような内容ｎ `.dockerignore` を作成して、不要なディレクトリやファイルを無視するようにしないといけません。
>
> ```text
> db
> html
> ```
>
