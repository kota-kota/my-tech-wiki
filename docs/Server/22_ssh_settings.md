---
title: SSHを設定する
---

## rootでのログインを禁止する

`/etc/ssh/sshd_config` に以下を追記・有効にします。

```text title="/etc/ssh/sshd_config"
PermitRootLogin no
PermitEmptyPasswords no
```

## SSHポート番号を変更する

SSHポート番号をデフォルト22から2022へ変更します。

`/etc/ssh/sshd_config` に `Port` の設定があるためこれを変更します。

```text title="/etc/ssh/sshd_config"
#Port 22
Port 2022
```

設定を反映します。

```bash
sudo systemctl restart sshd
```

### ファイアウォールで変更したポートを公開する

新規プロファイル `/etc/ufw/applications.d/myssh` （ファイル名は何でもいい）を作成して、以下のように記述します。

```text title="/etc/ufw/applications.d/myssh"
[myssh]
title=My SSH
description=This is used by My SSH
ports=2022/tcp
```

ファイアウォールで新ポート2022を公開し、旧ポート22(OpenSSH)は削除します。

```bash
sudo ufw allow from 192.168.0.0/24 to any app myssh
sudo ufw delete allow OpenSSH
```

`sudo ufw status` で状態を確認します。

```text
Status: active

To                         Action      From
--                         ------      ----
myssh                      ALLOW       192.168.0.0/24
```
