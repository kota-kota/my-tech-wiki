---
title: ファイルのバックアップ設定を行う
---

## rsyncを使用してファイルをバックアップする

```bash
rsync -v -r -p -o -g -t --exclude={除外ファイル or ディレクトリ} --delete --log-file={ログファイルパス} {同期元} {同期先}
```

- `-v (--verbose)` : 処理の経過を表示する
- `-r (--recursive)` : 再帰的にコピーする
- `-p (--perms)` : パーミッションを保持する
- `-o (--owner)` : 所有者をそのまま保持する
- `-g (--group)` : 所有グループをそのまま保持する
- `-t (--times)` : タイムスタンプを保持する
- `--exclude=` : ファイルやディレクトリを除外する
- `--delete` : 同期元にないファイルを同期先から削除する
- `--log-file=` : ログを書き出す

## crontabを使用してバックアップを定期的に実行する

rsyncコマンドを記載したシェルスクリプトを用意します。
以下は自分が実際に作成したシェルスクリプトです。

```text title="~/backup.sh"
#!/bin/bash
rsync -v -r -p -o -g -t --exclude='lost+found' --delete --log-file=~/backup_log/rsync-`date +"%Y%m%d-%H%M"`.log /mnt/nas_main/ /mnt/shared_data
```

上記シェルスクリプトを定期的に実行する設定を `cron.conf` に記載する。

```bash
echo '0 2 * * * ~/backup.sh' > cron.conf
```

`0 2 * * *` は、毎日午前2時に実行する設定になります。

`cron.conf` に書かれた設定を反映する。
`-u {ユーザ名}` は、指定したユーザで実行するオプションです。

```bash
crontab -u {ユーザ名} cron.conf
```

設定が反映されていることを確認する。

```bash
crontab -l
```
