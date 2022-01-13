---
title: Dockerを使ってみた
---

## コマンド

参考サイト - Dockerコマンド  
<https://qiita.com/k5n/items/2212b87feac5ebc33ecb>

### 省略系

`docker --help` で用意されているコマンドをみると、`Management Commands:` と `Commands:` の2種類ある。
`Commands:` は `Management Commands:` の省略系なので、複雑なことをやりたい場合は `Management Commands:` を使わないといけない。

| Commands利用  | Management Commands利用   |
| ------------- | ------------------------- |
| docker pull   | docker image pull         |
| docker images | docker image ls           |
| docker rmi    | docker image rm           |
| docker ps     | docker container ls       |
| docker ps -a  | docker container ls --all |
| docker rm     | docker container rm       |
| docker commit | docker container commit   |
| docker run    | docker container run      |
| docker start  | docker container start    |
| docker stop   | docker container stop     |

### version：バージョンを確認する

バージョンの確認で、簡易的な情報だけ表示します。

```bash
$ docker --version
Docker version 19.03.6, build 369ce74a3c
```

詳細な情報も全て表示します。

```bash
$ docker version
Client:
 Version:           19.03.6
 API version:       1.40
 Go version:        go1.12.17
 Git commit:        369ce74a3c
 Built:             Fri Dec 18 12:25:49 2020
 OS/Arch:           linux/arm64
 Experimental:      false

Server:
 Engine:
  Version:          19.03.6
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.17

～省略～
```

### info：環境情報を表示する

Dockerの環境情報を表示します。

```bash
$ docker info
Client:
 Debug Mode: false

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 19.03.6

～省略～
```

### images：取得済みイメージの一覧を表示する

取得済みイメージの一覧を表示します。

```bash
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              236f3028e59c        11 hours ago        126MB
```

上のコマンドは、`$ docker pull nginx` を実施した直後の結果です。

### ps：コンテナ一覧を表示する

実行中のコンテナ一覧を表示します。

```bash
$ docker ps
```

停止中も含めた全てのコンテナ一覧を表示します。

```bash
$ docker ps -a
```

### search：Docker Hub のイメージを検索する

Docker Hub にあるイメージを検索します。

```bash
$ docker search nginx
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
nginx                              Official build of Nginx.                        14645               [OK]
jwilder/nginx-proxy                Automated Nginx reverse proxy for docker con…   1998                                    [OK]
richarvey/nginx-php-fpm            Container running Nginx + PHP-FPM capable of…   812                                     [OK]
jc21/nginx-proxy-manager           Docker container for managing Nginx proxy ho…   168
linuxserver/nginx                  An Nginx container, brought to you by LinuxS…   142
（省略）
```

もっと詳しい使い方は以下に記載があります。  
<http://www.memotansu.jp/docker/771/>

### pull：Docker Hub からイメージを取得する

Docker Hub にあるイメージを取得します。

```bash
$ docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
6fcf2156bc23: Pull complete
31ca62f63eaf: Pull complete
1cd5998f3010: Pull complete
b2dd8627b82f: Pull complete
c4d503e83445: Pull complete
036b0382d2d7: Pull complete
Digest: sha256:bae781e7f518e0fb02245140c97e6ddc9f5fcf6aecc043dd9d17e33aec81c832
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

上のコマンドは、`$ docker search nginx` でヒットしたものです。

```bash
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
nginx                              Official build of Nginx.                        14645               [OK]
```

を取得しています。
未取得のイメージなのでダウンロードのログが出ていますが、取得済みであれば出ません。

### run：イメージからコンテナを生成して起動する

イメージからコンテナを作成して起動します。生成と起動を一度に行うコマンドです。

以下のコマンドはnginxイメージを例にしています。
`nginx/log` を作成しているのは、nginxのログをホスト側のディレクトリに出力するテストをするためです。

```bash
$ mkdir -p nginx/log
$ docker run -d -p 80:80 -v $(pwd)/nginx/log:/var/log/nginx --name webserver nginx
```

- `-d`：バックグラウンド実行することを指定しています。
- `-p 80:80`：ホスト側の80番ポートをコンテナ側の80番ポートに転送するように指定しています。`-p 8080:80` と指定すれば <http://localhost:8080> でアクセスできるようになります。
- `-v $(pwd)/nginx/log:/var/log/nginx`：ホストのディレクトリ `$(pwd)/nginx/log` をコンテナのディレクトリ `/var/log/nginx` としてマウントします。ホスト側のディレクトリも絶対パスで指定する必要があります。
- `--name webserver`：生成するコンテナの名前を指定します。指定しないとコンテナIDを利用することになります。
- `nginx`：コンテナ生成に使用するイメージの名前です。

本当は最後（イメージ名の後）に実行するコマンドを指定する のですが、nginx のイメージはデフォルトで nginx を起動するコマンドが実行されるようになっているため必要ありません。

<http://localhost> にアクセスすれば、"Welcome to nginx!"のページが表示されるはずです。

ホスト側でアクセスログ `$ cat nginx/log/access.log` が確認できます。

### exec：コンテナ内でコマンドを実行する

指定したコンテナで指定したコマンドを実行します。

コンテナ内でコマンドライン作業を実施してみます。

```bash
$ docker exec -it webserver /bin/bash
root@a9dbfe817edd:/#
```

- `-it`：コンテナ側のコマンドの入出力を手元のターミナルに接続します。
- `webserver`：コンテナの名前です。
- `/bin/bash`：実行するコマンドです。

上記コマンドを実行すると、root でログインした状態でコマンドプロンプトが表示されます。

`exec` で接続した場合は、`exit` してもコンテナは起動したままになります。

```bash
root@a9dbfe817edd:/# exit
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
a9dbfe817edd        nginx               "/docker-entrypoint.…"   6 minutes ago       Up 6 minutes        0.0.0.0:82->80/tcp   webserver
```

`-it`は、以下のようにコマンドを実行するだけであれば不要です。

```bash
$ docker exec webserver ls /etc/nginx
conf.d
fastcgi_params
koi-utf
koi-win
mime.types
modules
nginx.conf
scgi_params
uwsgi_params
win-utf
```

### start/stop：コンテナの起動/停止する

コンテナを起動・停止します。

`webserver` というコンテナを停止して、`docker ps` で確認してみます。

```bash
$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
a9dbfe817edd        nginx               "/docker-entrypoint.…"   10 minutes ago      Up 5 seconds        0.0.0.0:82->80/tcp   webserver
$ docker stop webserver
webserver
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
a9dbfe817edd        nginx               "/docker-entrypoint.…"   11 minutes ago      Exited (0) 18 seconds ago                       webserver
```

そして、再度起動してみます。

```bash
$ docker start webserver
webserver
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
a9dbfe817edd        nginx               "/docker-entrypoint.…"   12 minutes ago      Up 1 second         0.0.0.0:82->80/tcp   webserver
```

### commit：コンテナからイメージを作成する

コンテナからイメージを作成します。

イメージの命名規則は `<Docker Hubのユーザ名>/イメージ名:[タグ名]` ですが、Docker Hubに公開しない（`push`しない）なら、ユーザ名は不要です。

`webserver` コンテナから別のイメージ（`Welcome to nginx!` ではなく、`Hello, nginx!` を表示するもの）を作成してみます。
nginxのデフォルトのindex.htmlは、`/usr/share/nginx/html/index.html` にあるため、これを書き換えます。

`webserver` コンテナにある`/usr/share/nginx/html/index.html` をホスト側にコピーして、内容を書き換えます。
それを再度コンテナ側にコピーします。

```bash
$ docker cp webserver:/usr/share/nginx/html/index.html .
$ vim index.html
$ docker cp index.html webserver:/usr/share/nginx/html/index.html
$ docker exec webserver cat /usr/share/nginx/html/index.html
<!DOCTYPE html>
<html>
<head>
<title>Hello, nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Hello, nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

コンテナからイメージを作成します。

```bash
$ docker commit webserver webserver:0.0
sha256:c67e1239fe0b04d788867e38614a52b97b54802c2cbd5be3c8167a7a5558244c
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
webserver           0.0                 c67e1239fe0b        9 seconds ago       126MB
nginx               latest              236f3028e59c        12 hours ago        126MB
```

一度変更を消したいため、`webserver` コンテナを削除します。

```bash
$ docker stop webserver
$ docker rm webserver
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

`nginx` イメージから`webserver` コンテナを作成します。
`webserver:0.0` イメージから`webserver2` コンテナを作成します。

```bash
$ docker run -d -p 81:80 -v $(pwd)/nginx/log:/var/log/nginx --name webserver nginx
b19c47f0c3563181ed68eb7fbb44f8fc6244404cb6cc0ba77eabb537f9aa4690
$ docker run -d -p 82:80 -v $(pwd)/nginx2/log:/var/log/nginx --name webserver2 webserver:0.0
ad1be8216656c56a90fd4a1ce916c93275f364f705b4683447e4af6ff69d2984
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
ad1be8216656        webserver:0.0       "/docker-entrypoint.…"   4 seconds ago       Up 3 seconds        0.0.0.0:82->80/tcp   webserver2
b19c47f0c356        nginx               "/docker-entrypoint.…"   28 seconds ago      Up 27 seconds       0.0.0.0:81->80/tcp   webserver
```

<http://localhost:81> は"Welcome to nginx!"が表示され、<http://localhost:82> は"Hello, nginx!"が表示されるはずです。

### rm：コンテナを削除する

コンテナを削除します。

削除する前にコンテナを停止させます。いきなり削除もできますがちゃんとやります。

```bash
$ docker stop webserver
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
a9dbfe817edd        nginx               "/docker-entrypoint.…"   15 minutes ago      Exited (0) 3 seconds ago                       webserver
```

コンテナを削除します。
`docker ps -a` コマンドに表示されなくなります。

```bash
$ docker rm webserver
webserver
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

### rmi：イメージを削除する

イメージを削除します。

```bash
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              236f3028e59c        11 hours ago        126MB
$ docker rmi nginx
Untagged: nginx:latest
Untagged: nginx@sha256:bae781e7f518e0fb02245140c97e6ddc9f5fcf6aecc043dd9d17e33aec81c832
Deleted: sha256:236f3028e59c00fb4b99799e2b1e4edd9cc16febc199e456ac1537d6a729f580
Deleted: sha256:9f0a274fe79bb7668ee42c48f30fb7876b3ca311e125c2b9173bae799a09c1c3
Deleted: sha256:db874103219bb1721d653c0e5e207c2e91bc46409a6d598849d07f4b5ce3384b
Deleted: sha256:27e6b8253ff0e7a77c6d4ffa5af2d157c7b9eb7f4a1ca5323f761c0f79ae6be3
Deleted: sha256:0dcbd6e053c89abe2f79c268dab182e52bbd97ef88cb3f606447231903e8673b
Deleted: sha256:3bf0dc8405796d8db37ab2d672a770156e494b0f19e66294824bb609d4ae693e
Deleted: sha256:6e06900bc10223217b4c78081a857866f674c462e4f90593b01894da56df336d
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```
