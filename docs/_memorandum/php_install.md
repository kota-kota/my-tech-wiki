# PHPをインストールする

PHPをインストールする手順を記載します。

## UbuntuへPHPをインストールする

PHPをUbuntuへインストールする手順を記載します。

### インストール

`php7.2` と `libapache2-mod-php7.2` と `php7.2-xml` が最低限必要です。

```bash
$ sudo apt install php php-xml
```

### 動作確認

`/var/www/html/info.php` を作成して、中身を以下のようにします。

```text
<?php
echo phpinfo();
?>
```

<http://192.168.0.10/info.php> にアクセスしてみて`phpinfo()` の情報が表示されれば、Apacheでphpが正常に動作しています。

## WindowsへPHPをインストールする

PHPをWindowsへインストールする手順を記載します。

### インストール

以下のURLにアクセスして、ZIPファイルをダウンロードします。

- <https://windows.php.net/download/>  
  PHP 7.4 (7.4.16)：VC15 x64 Thread Safe (2021-Mar-02 17:13:59)

ZIPファイルを解凍し、任意のフォルダ `C:\php` に配置します。

PHPのコマンドを使えるようにするために、環境変数PathにPHP実行フォルダパス `C:\php` を追加します。

バージョン情報を表示させてみます。これで、`php` コマンドが使えるようになっているか確認します。

```bash
$ php -v
PHP 7.4.16 (cli) (built: Mar  2 2021 14:06:15) ( ZTS Visual C++ 2017 x64 )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
```

### 設定ファイルを修正

PHPの設定ファイルは、`C:\php\php.ini` にあります。
`php.ini` は初めは存在しないため、`php.ini-development` を `php.ini` にリネームします。

以下の設定を反映させます。

```text
extension_dir = "C:\php\ext"
default_charset = "UTF-8"
date.timezone = "Asia/Tokyo"
extension=mbstring
mbstring.language = Japanese
```

### Apacheと連携させる

ApacheでPHPを使えるようにするために、`httpd.conf` の最後に以下を追記しApacheを再起動します。

```text
PHPIniDir "C:/php"
LoadModule php7_module "C:/php/php7apache2_4.dll"
AddType application/x-httpd-php .php
```

### 動作確認

`C:\Apache24\htdocs\info.php` を作成して、中身を以下のようにします。

```text
<?php
echo phpinfo();
?>
```

<http://127.0.0.1/info.php> にアクセスしてみて `phpinfo()` の情報が表示されれば、Apacheでphpが正常に動作しています。
