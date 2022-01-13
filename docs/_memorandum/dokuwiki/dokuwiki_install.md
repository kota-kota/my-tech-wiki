# Dokuwikiをインストールする

DokuWikiをインストールする手順を記載します。

## 正式：Dockerイメージ利用してUbuntuへDokuwikiをインストールする

Docker Hubに、DokuWikiが既に導入済みのイメージがあるため、それを利用して導入します。

### 導入環境について

導入した環境は以下のとおりです。個人サーバとして利用しているシングルボードを使用します。

- サーバPC：ROCK64 single-board computer (ARM64)
- OS：Ubuntu 18.04.1 LTS (bionic)
- IPアドレス：192.168.0.10

Dockerイメージは以下を使用します。サーバPCがarm64アーキテクチャなので、arm64に対応している必要があります。

- Docker Hub - linuxserver/dokuwiki  
  <https://hub.docker.com/r/linuxserver/dokuwiki>

### 事前準備

```bash
$ mkdir -p /app/dokuwiki                          # 1
$ mkdir /app/dokuwiki/config                      # 2
$ chown www-data:www-data /app/dokuwiki/config    # 3
```

1. `docker-compose.yml` を配置するディレクトリ `/app/dokuwiki` を作成します。
1. ホスト側で保持しておくデータを格納するディレクトリ `/app/dokuwiki/config/` を作成します。
1. パーミッションをApacheユーザである `www-data` に変更します。

### docker-compose.ymlを作成する

以下のような内容で、`/app/dokuwiki/docker-compose.yml` を生成します。

```yaml
version: "2.1"
services:
  dokuwiki:
    image: ghcr.io/linuxserver/dokuwiki:arm64v8-latest
    container_name: dokuwiki
    environment:
    - PUID={uid}
    - PGID={gid}
    - TZ=Japan
    volumes:
    - {volume}:/config
    ports:
    - {http}:80
    - {https}:443
    restart: unless-stopped
```

- `{uid}`, `{gid}`：www-dataのユーザIDとグループIDを指定します。`$ id www-data` で取得可能です。
- `{volume}`：DokuWikiのデータを保持するためのホスト側のディレクトリを指定します。`/app/dokuwiki/config` を指定しました。
- `{http}`, `{https}`：ここで指定したポート番号をURLに指定することになります。httpは80、httpsは443でいいと思う。

### DokuWikiのコンテナを起動する

`docker-compose` コマンドを実行し、コンテナを作成し実行します。

```bash
$ docker-compose up -d
Pulling dokuwiki (ghcr.io/linuxserver/dokuwiki:arm64v8-latest)...
arm64v8-latest: Pulling from linuxserver/dokuwiki
3ba9ae63b35a: Pull complete
5568c4d76d06: Pull complete
8834797be162: Pull complete
88b01bee2fe6: Pull complete
cf644fb5afb6: Pull complete
82a0f62d2c8c: Pull complete
6b27701b8bf8: Pull complete
0f2fae9d47bb: Pull complete
e239f3177d40: Pull complete
1e5c8177ca31: Pull complete
Digest: sha256:9208bedd1e4c586fcb8c77fa8e0c02e334c6e070a09f367829db0ff3cbeb602d
Status: Downloaded newer image for ghcr.io/linuxserver/dokuwiki:arm64v8-latest
Creating dokuwiki ...
Creating dokuwiki ... done
```

<http://サーバーアドレス:ポート番号> にアクセスし、DokuWikiページが表示されることを確認します。
`admin/admin` でログインできます。

## 試用：UbuntuへDokuwikiをインストールする

Windowsへの導入に成功したため、サーバ運用するためにUbuntuへの導入を試みる。
本当はDockerを利用したいが、まずはホスト側のUbuntuへ導入してみたため、その手順をまとめておく。

### 導入環境について

導入した環境は以下のとおりです。

- サーバPC：ROCK64 single-board computer (ARM64)
- OS：Ubuntu 18.04.1 LTS (bionic)
- IPアドレス：192.168.0.10

事前に以下のソフトウェアをインストールしておく必要があります。
バージョンはお試しに導入してみたときのもので、絶対これでないといけないわけではないと思う。

- Apache 2.4.29
- PHP 7.4.16

### DokuWikiをダウンロードする

以下のURLからStableバージョンのDokuWikiファイルをダウンロードします。

- <http://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz>

ダウンロードには `wget` コマンドを使用します。

```bash
$ wget http://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz
```

ダウンロードしたDokuWikiファイルを解凍し、そのファイル一式をApacheディレクトリ `/var/www/dokuwiki` に配置します。
Apacheユーザ(www-data)から、更新やプラグインなどのインストールができるように、DokuWikiファイル一式をwww-dataユーザの権限でコピーします。

```bash
$ cd dokuwiki/    # ダウンロードして解凍したDokuWikiディレクトリ
$ sudo mkdir /var/www/dokuwiki    # ApacheディレクトリにDokuWikiディレクトリを作成
$ sudo chown www-data:www-data /var/www/dokuwiki/   # www-dataユーザの権限を付与
$ sudo -u www-data cp -a . /var/www/dokuwiki/     # www-dataユーザの権限でコピー
```

### ApacheでDokuWikiサイトを有効化する

<http://192.168.0.10/dokuwiki> のURLでアクセスして、`/var/www/dokuwiki` が読み込まれるようにします。

`/etc/apache2/sites-available` に `001-dokuwiki.conf` を以下の内容で作成します。

```text
Alias /dokuwiki /var/www/dokuwiki
<Directory /var/www/dokuwiki>
    Options FollowSymLinks
    AllowOverride All
    Order allow,deny
    Allow from all
</Directory>
```

`001-dokuwiki.conf` の設定を有効にして、問題なく反映できているかチェックします。
その後、Apacheを再起動します。

```bash
$ sudo a2ensite 001-dokuwiki    # 有効化
$ sudo apache2ctl configtest    # チェック
$ sudo service apache2 restart  # Apacheを再起動
```

## 試用：WindowsへDokuwikiをインストールする

サーバ環境(Ubuntu)へ導入する前に、試しにWindowsで導入してみたため、その手順をまとめておきます。

### 導入環境について

導入した環境は以下のとおり。

- Windows 10

事前に以下のソフトウェアをインストールしておく必要があります。
バージョンはお試しに導入してみたときのもので、絶対これでないといけないわけではないと思います。

- Apache 2.4.41
- PHP 7.4.16

### DokuWikiをダウンロードする

以下のURLへアクセスし、DokuWikiをダウンロードします。

- <https://www.dokuwiki.org/ja:dokuwiki>

以下のStableバージョンを選択しました。

- バージョン：2020-07-29 "Hogfather"
- ファイル名：dokuwiki-d597bb16624b692de7808b763b2abd86.tgz

解凍したフォルダをApacheフォルダ `C:\Apache24\htdocs\dokuwiki` に配置します。
Apacheをインストールした場所によって変わります。

### インストール（初期設定）

以下のURLにアクセスすると、DokuWikiの初期設定画面が表示されます。
Windowsなので、基本localhostですよね。

- <http://127.0.0.1/dokuwiki/install.php>

英語で分かりづらいので、右上の「Choose your language」で「ja」を選択し日本語表記にします。

必要事項を入力し、保存します。

その後、`install.php`を削除します。

再度、以下のURLへアクセスするとDokuWikiの画面になります。

- <http://127.0.0.1/dokuwiki>

### It seems your data directory is not properly secured.の警告を消す

ログインして管理ページへアクセスすると、赤枠のメッセージ「It seems your data directory is not properly secured.」が表示されます。これは、「dataディレクトリがブラウザからアクセスできてしまう」ということを警告しています。付属している `data/.htaccess` ファイルセキュリティ設定が読み込めてないため、Apacheの設定ファイル `httpd.cof` に以下を追加します。

```text
<Directory "${SRVROOT}/htdocs/dokuwiki">
   AllowOverride All
   Require all granted
</Directory>
```

再度アクセスすると赤枠メッセージが消える。キャッシュは削除したほうがいいかも。
