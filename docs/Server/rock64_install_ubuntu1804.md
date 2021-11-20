---
title: Rock64にUbuntu18.04をインストールする
tags:
  - Rock64
weight: 10
type: docs
---

Rock64にUbuntu18.04をインストールする手順を記載します。

## 動作に必要なもの

- 本体  
  ROCK64 Media Board Computer 4G DRAM
- 電源  
  AC/DC電源 5V/3A 3.5mm×1.35mm
- microSDカード  
  Samsung microSDHC 32GB EVOPlus Class10 UHS-I  
  ※相性があるようで、「pqi microSDHC 32GB Class10」で試したら、起動時に「Kernel Panic」になって起動しなかった。
- HDMIケーブル
- LANケーブル
- マウス  
  リモートログインできるようになるまで必要。何でもいいが、できれば有線がいいかも。
- キーボード  
  リモートログインできるようになるまで必要。何でもいいが、できれば有線がいいかも。

## Ubuntu18.04をインストールする

### ブート用イメージをSDカードに書きこむ

1. microSDカードをフォーマットします。フォーマットは `SD Card Formatter` というフリーソフトを使用しました。ファイルシステムは `FAT32` を選択します。
1. 以下のサイトから、`PINE64 Installler` をインストールします。
   - <https://github.com/pine64dev/PINE64-Installer>
1. `PINE64 Installler` を起動して、OS選択画面で `Ubuntu 18.04 Bionic minimal 64bit Image (build 0.7.9-1067)`を選択します。
   - これは、CUIのみのイメージです。
   - GUIが欲しい場合は「LXDE版」を選べばよさそう。
1. 「Flash」釦を選択し、書き込みを開始します。

### 起動する

ブート用イメージを書き込んだmicroSDカードを差し込み、電源を差します。
無事に起動すれば成功です。

### ログインする

初期のユーザとパスワードは、「rock64/rock64」になります。

## 初期設定

### パッケージ一覧を更新

`apt-get` コマンドを使って、インストール可能なパッケージ一覧を更新します。

```bash
$ sudo apt-get update   # インストール可能なパッケージ一覧を更新
$ dpkg -l               # インストール済みパッケージを確認
```

### 日本に対応する

#### キーボードレイアウトを変更する

キーボードレイアウトを日本語に変更します。

```bash
$ sudo dpkg-reconfigure keyboard-configuration
# コンフィグ画面が表示される
```

コンフィグ画面では、以下の順に選択していきます。

1. 「Generic 105-key PC (intl.)」
1. 「Japanese」
1. 「Japanese」
1. 「The default for the keyboard layout」
1. 「No compose key」

#### ロケールを変更する

ロケールを `ja_JP.UTF-8` に変更します。

```bash
$ sudo dpkg-reconfigure locales
# コンフィグ画面が表示される
```

コンフィグ画面では、スペースキーでチェックボックスに対して入力します。

#### タイムゾーンを変更する

タイムゾーンを日本に変更します。

```bash
$ timedatectl set-timezone Asia/Tokyo
```

### ユーザを追加する

`adduser` コマンドを使います。
同じようなコマンドで `useradd` コマンドがありますが、ホームディレクトリを作ってくれないため使用しません。

```bash
$ cat /etc/passwd                   # ユーザ一覧
$ sudo adduser {ユーザ名}           # 新規ユーザ追加
$ sudo gpasswd -a {ユーザ名} sudo   # sudo権限付与（sudoグループに追加）
$ su - {ユーザ名}                   # ユーザ切り替え
```

### 固定IPアドレスを設定する

OSは Ubuntu18.04 なので、ネットワーク設定方法は `netplan` を使用します。
`netplan` については以下のページを参照してください。

- [Ubuntuのネットワーク接続コマンド - netplan](../ubuntu/2021-05-01-ubuntu_network_netplan.md)

#### 現在のネットワーク設定を確認する

```bash
$ ip addr      # ネットワークインターフェース名とIPアドレスを確認する
$ route -n     # ゲートウェイを確認する
$ sudo systemd-resolve --status | grep "DNS Servers"  # DNSネームサーバの確認
```

#### ネットワーク設定を変更する

初期設定である `eth0.yaml` を残しつつ、設定を変更したいため、以下のような操作を行います。

- `eth0.yaml` をDHCP設定用のものとして、`00_eth0_dhcp.yaml` にリネームします。
- 固定IPアドレス設定ファイルとして、`01_eth0_static.yaml` を作成します。

以下はそのときのコマンド入力です。

```bash
$ cd /etc/netplan
$ sudo mv eth0.yaml 00_eth0_dhcp.yaml                 # 初期設定を取っておく
$ sudo cp -fpr 00_eth0_dhcp.yaml 01_eth0_static.yaml  # 固定IPアドレス設定ファイルを作成
```

固定IPアドレス設定として、以下の内容を `01_eth0_static.yaml` に反映します。

- ネットワークインタフェース： eth0
- アドレス： 192.168.0.10
- ネットマスク： 255.255.255.0（ビット指定だと「/24」）
- ゲートウェイ： 192.168.0.1
- DNSネームサーバ： 192.168.0.1

```yaml
network:
version: 2
renderer: networkd
ethernets:
eth0:
    addresses: [192.168.0.10/24]
    gateway4: 192.168.1.1
    nameservers:
    addresses: [192.168.1.1]
    search: []
    optional: true
```

### リモートログインできるようにする

リモートログインしてRock64を操作するために、SSHを導入します。

`openssh-server` をインストールするだけです。

```bash
$ sudo apt-get install openssh-server
```

SSHログインができるか試します。ログインできれば、マウスとキーボードは不要になります。

## 共有フォルダ

sambaをインストールします。

```bash
$ sudo apt-get install samba
```

sambaグループにユーザ登録します。

```bash
$ sudo pdbedit -a <user_name>
```

以下は実行例です。

```bash
$ sudo pdbedit -a odani
new password:
retype new password:
Unix username:        odani
NT username:
Account Flags:        [U          ]
User SID:             S-1-5-21-850546626-2320128605-3607607440-1000
Primary Group SID:    S-1-5-21-850546626-2320128605-3607607440-513
Full Name:
Home Directory:       \\rock64\odani
HomeDir Drive:
Logon Script:
Profile Path:         \\rock64\odani\profile
Domain:               ROCK64
Account desc:
Workstations:
Munged dial:
Logon time:           0
Logoff time:          水, 06  2月 2036 15:06:39 UTC
Kickoff time:         水, 06  2月 2036 15:06:39 UTC
Password last set:    月, 21  9月 2020 06:13:46 UTC
Password can change:  月, 21  9月 2020 06:13:46 UTC
Password must change: never
Last bad password   : 0
Bad password count  : 0
Logon hours         : FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
```

設定ファイルを変更します。

```text
[nas]
comment = Network Attached Storage
path = /mnt/nas_main
public = yes
writable = yes
guest ok = yes
create mask = 0777
```

なんか思考錯誤中。。
