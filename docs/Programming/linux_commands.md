# Linuxコマンド

## ファイル操作系

### ディレクトリに存在するファイル数をカウントする

```bash
ls -Ua1 | wc -l
```

### grepの検索結果をファイルパスだけにする

```bash
grep -rl "検索文字列" ./*
```

### grepの検索結果に色と行数を付ける

```bash
grep --color=auto -n
```

### 指定したディレクトリ配下の全ファイルを一括操作する

例：ファイルのパーミッションを `664` に一括で変更する

```bash
find ./ -type f -print0 | xargs -0 chmod 664
```

例：ディレクトリのパーミッションを `755` に一括で変更する

```bash
find ./ -type d -print0 | xargs -0 chmod 755
```

## ユーザ管理系

### 現在ログインしているユーザを表示する

```bash
who
```

### ユーザ一覧を表示する

```bash
cat /etc/passwd
```

### グループ一覧を表示する

```bash
cat /etc/group
```

### ユーザのグループ一覧を表示する

```bash
groups ユーザ名
```

### グループ名を変更する

```bash
groupmod -n {変更後グループ名} {変更前グループ名}
```

### グループにユーザを登録する

```bash
gpasswd -a {ユーザ名} {グループ名}
```

### グループに誰が所属しているか確認する

```bash
getent group {グループ名}
```
