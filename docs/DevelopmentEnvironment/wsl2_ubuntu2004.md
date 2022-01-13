# Windows 10 に WSL2 + Ubuntu 20.04 LTS を導入する

## WSL2 を有効にして、Ubuntu 20.04 LTS を導入する

以下のサイトを参考に。既に有効にしてしまっているため省略します。

<https://docs.microsoft.com/ja-jp/windows/wsl/install-win10>

### Linux 用 Windows サブシステムを有効にする

管理者として PowerShell を開き、以下を実行します。

```powershell
> dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

### 仮想マシンの機能を有効にする

管理者として PowerShell を開き、以下を実行します。

```powershell
> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

### Linux カーネル更新プログラムパッケージをダウンロードする

- 最新のパッケージをダウンロードします。  
  [x64 マシン用 WSL2 Linux カーネル更新プログラム パッケージ](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)
- 更新プログラムパッケージを実行します。

ここで一度再起動します。

### WSL2 を既定のバージョンとして設定する

PowerShell を開き、以下を実行します。

```powershell
> wsl --set-default-version 2
```

### Linux ディストリビューションをインストールする

Microsoft Store を開き、希望する Linux ディストリビューションを選択します。今回は「Ubuntu20.04 LTS」を選択します。

インストール完了後、初めて起動するとコンソールウィンドウが開き、ファイルが圧縮解除されて PC に格納されるまで待つように求められます。今後のすべての起動には時間はかかりません。

ユーザー アカウントとパスワードを作成します。

```bash
Installing, this may take a few minutes...
Please create a default UNIX user account. The username does not need to match your Windows username.
For more information visit: https://aka.ms/wslusers
Enter new UNIX username: Kyohei
adduser: Please enter a username matching the regular expression configured
via the NAME_REGEX[_SYSTEM] configuration variable.  Use the `--force-badname'
option to relax this check or reconfigure NAME_REGEX.
Enter new UNIX username: Kyohei
adduser: Please enter a username matching the regular expression configured
via the NAME_REGEX[_SYSTEM] configuration variable.  Use the `--force-badname'
option to relax this check or reconfigure NAME_REGEX.
Enter new UNIX username: # ユーザ名
New password: # パスワード
Retype new password: # パスワード
passwd: password updated successfully
Installation successful!
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.72-microsoft-standard-WSL2 x86_64)

```

## 導入後に実施すること

### WSL のバージョンを確認する

インストールされている各 Linux ディストリビューションに割り当てられている WSL バージョンを確認します。VERSION が 2 になっていればOKです。

```powershell
> wsl --list --verbose
  NAME            STATE           VERSION
* Ubuntu-20.04    Running         2
```

### OS のシステムを更新する

```bash
$ sudo apt update
$ sudo apt upgrade
```

### SSH キーを GitHubへ登録する

SSH クライアントを使用するため、`openssh-client` をインストールします。

```bash
$ sudo apt install openssh-client
```

SSH キーを生成し、`id_rsa.pub` の内容を GitHub へ登録します。

```bash
$ ssh-keygen -t rsa -b 4096
$ cat ~/.ssh/id_rsa/pub
```

## WSL2 で Docker Desktop for Windows を使う

Docker公式サイトからDocker Desktop for Windowsをダウンロードしインストールを実行します。「Get Docker Desktop for Windows」をクリックします。

- <https://docs.docker.com/docker-for-windows/wsl/#download>

インストール手順です。

- 「Install required Windows components for WSL2」にチェックを入れます。
- しばらくするとインスト－ルが終了し、ログアウトを求められます。
- ログインすると、Docker Desktop が自動で起動します。
- Tutorial はスキップします。

設定画面で「Use the WSL2 based engine」が有効になっていることを確認します。

そのあと「Resources」->「WSL INTEGRATION」を選択し、「Enables integration with additional distros:」以下に表示されているLinuxからDockerを使用するものの設定を有効にし、「Apply & Restart」ボタンを押します。

- 今回は「Ubuntu-20.04」を有効にします。

Ubuntu-20.04 のコンソールを表示して、Docker Desktop が使えるかどうか確認します。

```bash
# Docker のバージョンを確認します
$ docker --version
Docker version 20.10.7, build f0df350

# 'hello-world'イメージからコンテナを起動します
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
b8dfde127a29: Pull complete
Digest: sha256:9f6ad537c5132bcce57f7a0a20e317228d382c3cd61edae14650eec68b2b345c
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

# イメージを確認します
$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   4 months ago   13.3kB

# コンテナを確認します
$ docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
a18652097879   hello-world   "/hello"   44 seconds ago   Exited (0) 43 seconds ago             unruffled_lamport

# コンテナを削除します
$ docker rm unruffled_lamport
unruffled_lamport

# コンテナが削除されていることを確認します
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

# イメージを削除します
$ docker rmi hello-world
Untagged: hello-world:latest
Untagged: hello-world@sha256:9f6ad537c5132bcce57f7a0a20e317228d382c3cd61edae14650eec68b2b345c
Deleted: sha256:d1165f2212346b2bab48cb01c1e39ee8ad1be46b87873d9ca7a4e434980a7726
Deleted: sha256:f22b99068db93900abe17f7f5e09ec775c2826ecfe9db961fea68293744144bd

# イメージが削除されていることを確認します
$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

## Ubuntu 20.04 イメージを使ってみる

```docker
FROM ubuntu:20.04

RUN sed -i 's@archive.ubuntu.com@ftp.jaist.ac.jp/pub/Linux@g' /etc/apt/sources.list

# Update
RUN apt update

# Install LLVM
RUN apt install --no-install-recommends -y clang-11
```

```bash
$ docker build -t my_ubuntu:1.0 .
$ docker run --rm -v `pwd`:`pwd` -it my_ubuntu:1.0 /bin/bash
```
