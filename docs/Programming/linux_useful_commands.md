---
title: Linuxの便利なコマンド
---

## 指定したディレクトリ配下のパーミッションを一括で変更する

ファイルのパーミッションを `664` に一括で変更する

```bash
find ./ -type f -print0 | xargs -0 chmod 664
```

ディレクトリのパーミッションを `755` に一括で変更する

```bash
find ./ -type d -print0 | xargs -0 chmod 755
```
