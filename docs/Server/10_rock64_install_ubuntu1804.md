---
title: Rock64にUbuntu18.04をインストールする
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
  <https://github.com/pine64dev/PINE64-Installer>
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
sudo apt-get update
```

インストール済みパッケージを確認します。

```bash
dpkg -l
```

### 日本に対応する

#### キーボードレイアウトを変更する

キーボードレイアウトを日本語に変更します。

```bash
sudo dpkg-reconfigure keyboard-configuration
```

上記コマンドを入力するとコンフィグ画面が表示されます。
コンフィグ画面では、以下の順に選択していきます。

1. 「Generic 105-key PC (intl.)」
1. 「Japanese」
1. 「Japanese」
1. 「The default for the keyboard layout」
1. 「No compose key」

#### ロケールを変更する

ロケールを `ja_JP.UTF-8` に変更します。

```bash
sudo dpkg-reconfigure locales
```

上記コマンドを入力するとコンフィグ画面が表示されます。
コンフィグ画面では、スペースキーでチェックボックスに対して入力します。

#### タイムゾーンを変更する

タイムゾーンを日本に変更します。

```bash
timedatectl set-timezone Asia/Tokyo
```

### ユーザを追加する

`adduser` コマンドを使います。
同じようなコマンドで `useradd` コマンドがありますが、ホームディレクトリを作ってくれないため使用しません。

新規ユーザを追加します。

```bash
sudo adduser {ユーザ名}
```

sudoグループにユーザを追加して、sudo権限を付与します。

```bash
sudo gpasswd -a {ユーザ名} sudo
```

ユーザ一覧を確認して、作成したユーザが存在することを確認します。

```bash
cat /etc/passwd
```

### 固定IPアドレスを設定する

Ubuntu18.04 なので、ネットワーク設定は `netplan` を使用します。

#### 現在のネットワーク設定を確認する

ネットワークインターフェース名とIPアドレスを確認します。

```bash
ip addr
```

ゲートウェイを確認します。

```bash
route -n
```

DNSネームサーバの確認します。

```bash
sudo systemd-resolve --status | grep "DNS Servers"
```

#### ネットワーク設定を変更する

初期設定である `eth0.yaml` を残しつつ設定を変更したいため、以下のような操作を行います。

- `eth0.yaml` をDHCP設定用のものとして、`00_eth0_dhcp.yaml` にリネームします。
- 固定IPアドレス設定ファイルとして、`01_eth0_static.yaml` を作成します。

以下のようなコマンド入力になります。

```bash
cd /etc/netplan
sudo mv eth0.yaml 00_eth0_dhcp.yaml
sudo cp -fpr 00_eth0_dhcp.yaml 01_eth0_static.yaml
```

固定IPアドレスの設定を `01_eth0_static.yaml` に反映します。

```yaml title="/etc/netplan/01_eth0_static.yaml"
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

上記は以下のような内容を反映しています。

- ネットワークインタフェース： eth0
- アドレス： 192.168.0.10
- ネットマスク： 255.255.255.0（ビット指定だと「/24」）
- ゲートウェイ： 192.168.0.1
- DNSネームサーバ： 192.168.0.1

!!! note "yamlファイルの読み込み優先順"
    複数のYAMLファイルを配置すると、ファイル名を辞書順に読み込んで設定がマージされます。
    そのため、ファイル先頭に連番を振り、設定が上書きされるようにします。
    例えば、`00-installer-config.yaml` と `01-my-config.yaml` を配置すると、`01-my-config.yaml` の設定が優先されます。

#### ネットワーク設定を反映する

以下のコマンドを入力して、設定を適用します。

```bash
netplan apply
```

### リモートログインできるようにする

リモートログインしてRock64を操作するために、`openssh-server` をインストールします。

```bash
sudo apt-get install openssh-server
```

SSHログインができるか試します。
ログインできれば、マウスとキーボードは不要になります。

## 共有フォルダを設定する

`samba` をインストールします。

```bash
sudo apt-get install samba
```

sambaの設定ファイルを変更します。

```text title="/etc/samba/smb.conf"
[nas]
   comment = Network Attached Storage
   path = /mnt/nas_main
   force user = odani
   force group = odani
   create mode = 0664
   directory mode = 0755
   writable = yes
   guest ok = no
   public = yes

[shared]
   comment = Shared Data
   path = /mnt/shared_data
   force user = odani
   force group = odani
   create mode = 0664
   directory mode = 0755
   writable = yes
   guest ok = no
   public = yes
```

設定を反映します。

```bash
sudo service smbd restart
```
