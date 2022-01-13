# Apacheをインストールする

Apacheをインストールする手順を記載します。

## UbuntuへApacheをインストールする

ApacheをUbuntuへインストールする手順を記載します。

### インストール

`apt` コマンドを使用してインストールします。

```bash
$ sudo apt install apache2
```

バージョンを確認したら、「Apache 2.4.29」でした。

```bash
$ apache2 -v
Server version: Apache/2.4.29 (Ubuntu)
Server built:   2019-04-03T13:22:37
```

### 動作確認

以下のコマンドを実行してエラーが出ないことを確認します。

```bash
$ sudo apache2ctl configtest
Syntax OK
```

<http://192.168.0.10> にアクセスして、Apacheのデフォルトページが表示されたら成功です。

### Apacheの各種設定を変更する

#### rewriteを有効にする

Apacheのデフォルトでは `rewrite` は無効になっていますので有効にします。
DokuWikiなどのサービスを利用する場合には、有効にしておく必要があります。

```bash
$ sudo a2enmod rewrite
$ sudo service apache2 restart
```

#### AH00558のエラーが出るときの対処

`apache2ctl configtest` などのコマンドで、以下のようなエラーが出る場合があります。

```text
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
```

このエラーを解消するための手順を記載します。

`/etc/apache2/conf-available/fqdn.conf` を新規作成し、以下の内容を記載します。

```text
ServerName myserver
```

以下のコマンドを実行して、設定を有効にします。

```bash
$ sudo a2enconf fqdn
$ sudo service apache2 restart
```

エラーが解消されているはずです。

## WindowsへApacheをインストールする

ApacheをWindowsへインストールする手順を記載します。

### インストール

以下のURLへアクセスして、Apacheのビルド済パッケージをダウンロードします。

- <https://www.apachelounge.com/download/>  
  httpd-2.4.46-win64-VS16.zip

Apacheのインストールは、ダウンロードしたZIPファイルを展開し任意の場所に配置するだけです。
ZIPファイルを展開すると `Apache24` というフォルダがあるので、その中にあるフォルダ・ファイル群をインストールしたい場所 `C:\Apache64` に移動させます。

Apacheのコマンドを使えるようにするために、環境変数PathにApache実行フォルダパス `C:\Apache64\bin` を追加します。

バージョン情報を表示させてみます。これで、`httpd` コマンドが使えるようになっているか確認します。

```bash
$ httpd -v
Server version: Apache/2.4.41 (Win64)
Apache Lounge VS16 Server built:   Aug  9 2019 16:46:32
```

### 設定ファイルを修正

Apacheの設定ファイルは、`C:\Apache64\conf\httpd.conf` にあります。
この設定ファイルに、以下の設定を反映させます。

- `SRVROOT`：Apacheを配置したパスに修正します。
- `ServerName`：設定しないと、`Could not reliably determine the server’s fully qualified domain name…`と表示されて、エラーになってしまう場合があります。

```text
Define SRVROOT "c:/Apache24"

#ServerName www.example.com:80
ServerName localhost:80
```

### 動作確認

Apacheをサービスとして起動します。
コマンドプロンプトから `httpd` コマンドを使って操作できます。

※コマンドプロンプトは「管理者として実行」する必要があります。

```bash
# ApacheをWindowsサービスに登録する
$ httpd -k install

# ApacheをWindowsサービスから削除する
$ httpd -k uninstall

# Apacheの起動・停止・再起動
$ httpd -k start
$ httpd -k stop
$ httpd -k restart
```

Windowsの管理ツールからも操作可能です。「コントロールパネル」->「管理ツール」->「サービス」を表示して、サービス名が「Apache24」となっている行で右クリックすると、起動、停止、再起動などが行えます。

<http://127.0.0.1> にアクセスして、Apacheのデフォルトページが表示されれば成功です。

### Apacheの各種設定を変更する

Apacheの設定ファイルは `httpd.conf` です。
各種設定の変更方法を記載します。

#### rewrite機能を有効にする

```text
LoadModule rewrite_module modules/mod_rewrite.so
```

#### ディレクトリアクセス時の対象ファイルをindex.html以外も対象にする

```text
<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule>
```
