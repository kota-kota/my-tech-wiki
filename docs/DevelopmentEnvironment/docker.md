---
title: Dockerをインストールする
---

## Windows

工事中

## Ubuntu

### インストール

公式手順は以下にあります。  
<https://docs.docker.com/engine/install/ubuntu/>

Docker Engineの実行に必要なパッケージ一式をインストールします。

```bash
sudo apt install \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg-agent \
      software-properties-common
```

Dockerオフィシャルサイトのキーをダウンロードして登録します。

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Dockerダウンロードサイトをaptリポジトリに追加します。

```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

Docker Engineをインストールします。

```bash
sudo apt install docker-ce docker-ce-cli containerd.io
```

### ユーザをdockerグループに参加させる

Dockerのプロセスは、dockerグループまたはroot権限を持ったユーザのみ制御することができます。
しかし、Dockerを制御するたびにsudoコマンドを打つのは面倒です。
また、root権限で作業しているとdockerグループ権限を間違って行使してしまう可能性があります。

そのため、ubuntuユーザーでもdocker操作できるようにしておきます。
この後、再起動が必要です。

```bash
sudo gpasswd -a {ユーザ} docker
```
