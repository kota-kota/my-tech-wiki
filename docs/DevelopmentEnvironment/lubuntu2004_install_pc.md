# PC に Lubuntu 20.04 LTS を導入する

## Lubuntu のインストール

### Lubuntu のISOファイルをダウンロードする

以下のサイトから、`lubuntu-20.04.4-desktop-amd64.iso` をダウンロードします。

<https://cdimage.ubuntu.com/lubuntu/releases/20.04.4/release/>

### USBブートメディアを作成する

ISOファイルをUSBに書き込むために、`Rufus` というソフトを使用します。
以下のサイトから、`Rufus 3.17 Portable` をダンロードします。

<https://rufus.ie/ja/>

- 「デバイス」の項目にUSBメモリが表示されているかを確認します。
- 「選択」をクリックしてISOファイルを選択します。
- 「スタート」をクリックします。

### PCにインストールする

PCにUSBメモリを挿入します。

PCのBIOSを起動し、外部デバイスから起動させるように設定を変更し、再起動します。

Lubuntuが起動します。
デスクトップにある「Install Lubuntu 20.04 LTS」を起動します。

あとは対話形式で必要事項を入力・選択していきます。

- 「日本語」を選び，「次へ」をクリック
- 地域として「Asia」， ゾーンとして「Tokyo」， を選び， 「次へ」をクリック
- キーボードを選び， 「進む」をクリック
- 「ディスクの消去」を選び 「次へ」をクリック
- 「あなたのフルネーム」（初期ユーザのフルネーム）と， 「ログイン」（初期ユーザのユーザ名）と， コンピュータの名前 （＝「ホスト名」と 「パスワード」（初期ユーザのパスワード）を設定し， 「進む」をクリック
- 「インストール」をクリック

## 設定

### インストール可能なパッケージの一覧を更新する

```bash
sudo apt update
```

### ホームディレクトリに存在するディレクトリを英語に変更する

`xdg-user-dirs-gtk` がLubuntuにはインストールされていないため、インストールします。

```bash
sudo apt install xdg-user-dirs-gtk
```

以下のコマンドを実行します。

```bash
LANG=C xdg-user-dirs-gtk-update
```

ウィンドウが出てくるので、「Don't ask me this again」にチェックを入れ、「Update Names」をクリックします。

### ホームディレクトリに存在するディレクトリを削除する

初期ディレクトリを削除しても復元されないように `/etc/xdg/user-dirs.conf` を以下のように変更します。

```text
#enabled=True
enabled=False
```

変更後、要らないディレクトリを削除します。

```bash
rm -fr Desktop/ Documents/ Downloads/ Music/ Pictures/ Public/ Templates/ Videos/
```

### ノートPCで蓋を閉じてもスリープにしない

`/etc/systemd/logind.conf` に下記の行を変更します。

```text
- #HandleLidSwitch=suspend
+ HandleLidSwitch=ignore
```

変更内容を反映します。

```bash
systemctl restart systemd-logind
```

### リモートログインできるようにする

`openssh-server` をインストールします。

```bash
sudo apt-get install openssh-server
```

### 固定IPアドレスを設定する

`/etc/netplan` に設定ファイル `99-networkd-static.yaml` を追加します。

```yaml
network:
  version: 2
  renderer: networkd
  dhcp4: false
  dchp6: false
  ethernets:
    enp3s0:
      addresses: [192.168.0.20/24]
      gateway4: 192.168.0.1
      nameservers:
        addresses: [192.168.0.1]
```

設定を反映します。

```bash
sudo netplan apply
```

### Dockerをインストールする

公式手順は以下にあります。  
<https://docs.docker.com/engine/install/ubuntu/>

Docker Engineの実行に必要なパッケージ一式をインストールします。

```bash
sudo apt-get install ca-certificates curl gnupg lsb-release
```

Docker公式のGPGキーをダウンロードして登録します。

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

`stable` リポジトリを設定します。

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

パッケージの一覧を更新します。

```bash
sudo apt-get update
```

Docker Engine をインストールします。

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

`hello-world` イメージを実行してみます。

```bash
sudo docker run hello-world
```
