# Sambaで共有フォルダを設定する

!!! attention
    修正必要

`samba` について記載します。

## インストール

Ubuntuであれば `apt` コマンドでインストールできます。

```bash
$ sudo apt update
$ sudo apt install samba
```

## Sambaのユーザ情報に追加

Sambaのユーザ情報に、Linuxのユーザを追加します。

```bash
$ sudo smbpasswd -a <user_name>
New SMB password: [パスワード入力]
Retype new SMB password: [パスワード入力]
Added user <user_name>.
```

## 設定ファイル

設定ファイルは、`/etc/samba/smb.conf` にあります。

### 例：フルアクセス設定

誰でもアクセスできるフォルダ `/var/tmp` を設定します。
`/var/tmp` は初めから作成されています。

```text
[temp]
   comment = Temporary Data Space
   path = /var/tmp
   force user = {USER}
   force group = {GROUP}
   create mode = 0644
   directory mode = 0755
   writable = yes
   browsable = yes
   guest ok = yes
   guest only = yes
```

### 例：ユーザ限定アクセス設定

ユーザを限定してアクセスできるフォルダ `/var/share` を設定します。

```text
[share]
   comment = Share Data Space
   path = /var/share
   force user = {USER}
   force group = {GROUP}
   create mode = 0644
   directory mode = 0755
   writable = yes
   browsable = yes
```
