# Gitの環境設定

Gitの環境設定について記載します。
Gitの環境設定は、`.gitconfig` に記載されます。

## コンフィグを設定する

### ユーザ名とメールアドレスを設定する

```bash
$ git config --global user.name "Name"
$ git config --global user.email mail@example.com
```

`--global` を指定した場合、`.gitconfig` には以下が追記されます。

```text
[user]
    name = Name
[user]
    email = mail@example.com
```

### チェックアウト時とコミット時に改行コードを変換しない

Git for Windowsに対する設定です。

```bash
$ git config --global core.autocrlf false
```

`--global` を指定した場合、`.gitconfig` には以下が追記されます。

```text
[core]
    autoCRLF = false
```

### 日本語のファイル名を表示する

```bash
$ git config --local core.quotepath false    #リポジトリ内のみ
$ git config --global core.quotepath false   #全体
```

`--global` を指定した場合、`.gitconfig` には以下が追記される。

```text
[core]
    quotepath = false
```

## エイリアスを作成する

### コミットログを簡潔に表示する

```text
[alias]
    plog = log --pretty='format:%C(yellow)%h %C(green)%cd %C(reset)%s %C(red)%d %C(cyan)[%an]' --date=iso
```
