# Ubuntuの様々な設定まとめ

## sudo入力後のコマンド補完を有効にする

`~/.bashrc` に以下を記載します。

```bash
complete -cf sudo
```

## ノートPCで蓋を閉じてもスリープにしない

`/etc/systemd/logind.conf` に下記の行を変更して、設定を反映します。

```bash
$ vim /etc/systemd/logind.conf
- #HandleLidSwitch=suspend
+ HandleLidSwitch=ignore
$ systemctl restart systemd-logind
```
