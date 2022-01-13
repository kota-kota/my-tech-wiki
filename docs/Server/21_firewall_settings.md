# ファイアウォールを設定する

## 簡易ファイアウォール(ufw)

パケットフィルタリングできるコマンド `iptables` を使用すると細かい設定ができるが難しいため、簡易的なファイアウォールを提供する `ufw` コマンドを使用します。

### ファイアウォールを有効にする

ファイアウォールの状態を確認します。
`Status: inactive` が無効で、`Status: active` が有効になります。

```bash
sudo ufw status
```

ファイアウォールを有効にします。

```bash
sudo ufw enable
Firewall is active and enabled on system startup
```

!!! note
    `Command may disrupt existing ssh connections. Proceed with operation (y|n)?`
    と聞かれます。SSH接続が出来なくなるけど大丈夫か？という質問なので、`y` と入力して問題ありません。

ファイアウォールが利用可能なアプリケーションを調べます。

```bash
sudo ufw app list
```

インストールしているアプリによりますが、例えば以下のような結果になります。

```text
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
  Samba
```

### Apacheのアクセスを許可する

Apacheはhttpのみ（ポート80）、Apache Fullはhttpとhttps（ポート443）、Apache Secureはhttpsのみを表しています。
いずれSSL化したいため、Fullで許可しておきます。

```bash
sudo ufw allow 'Apache Full'
```

`sudo ufw status` で状態を確認します。

```text
To                         Action      From
--                         ------      ----
Apache Full                ALLOW       Anywhere
Apache Full (v6)           ALLOW       Anywhere (v6)
```

#### IPv6を無効にする

Apacheのアクセスを許可すると、IPv4だけでなくIPv6もアクセス許可されます。
IPv6は無効にしたいため、`/etc/default/ufw` を以下のように変更します。

```text title="/etc/default/ufw" linenums="10"
# Set to yes to apply rules to support IPv6 (no means only IPv6 on loopback
# accepted). You will need to 'disable' and then 'enable' the firewall for
# the changes to take affect.
#IPV6=yes
IPV6=no
```

`sudo ufw status` で状態を確認します。

```text
To                         Action      From
--                         ------      ----
Apache Full                ALLOW       Anywhere
```

### OpenSSHのアクセスを許可する

OpenSSHは、LAN内からのアクセスのみを許可し、それ以外は許可しないようにします。
`ufw limit OpenSSH` でlimitをかける方法もありますが、外部アクセスが必要になったときに考えることにします。

```bash
sudo ufw allow from 192.168.0.0/24 to any app OpenSSH
```

`sudo ufw status` で状態を確認します。

```text
To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       192.168.0.0/24
```

#### SSHポート番号を変更する

SSHポート番号をデフォルト22から2022へ変更します。

`/etc/ssh/sshd_config` のバックアップを取り、以下を追記します。

```text
#Port 22
Port 2022
```

sshを再起動。

```bash
sudo systemctl restart sshd
```

#### ファイアウォールで新ポート2022を公開する

まず、プロファイルを作成します。
`/etc/ufw/applications.d/myssh` を新規作成して、以下のように記述します。

```text
[myssh]
title=My SSH
description=This is used by My SSH
ports=2022/tcp
```

ファイアウォールで新ポート2022を公開します。

```bash
sudo ufw allow from 192.168.0.0/24 to any app myssh
```

旧ポート22(OpenSSH)は削除します。

```bash
sudo ufw delete allow OpenSSH
```

`sudo ufw status` で状態を確認します。

```bash
$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
myssh                      ALLOW       192.168.0.0/24
```

### Sambaのアクセスを許可する

Sambaは、LAN内からのアクセスのみを許可し、それ以外は許可しないようにします。

```bash
sudo ufw allow from 192.168.0.0/24 to any app Samba
```

`sudo ufw status` で状態を確認します。

```text
To                         Action      From
--                         ------      ----
Samba                      ALLOW       192.168.0.0/24
```
