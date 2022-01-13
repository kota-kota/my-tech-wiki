# Gitコマンド

Gitコマンドの使い方をまとめておきます。

## git clone

### リポジトリを複製する

リモートリポジトリ `{remote}` を、現在のディレクトリに複製します。

```bash
$ git clone {remote}
```

リモートリポジトリ `{remote}` を、指定したディレクトリ `{dir}` に複製します。

```bash
$ git clone {remote} {dir}
```

リモートリポジトリ `{remote}` にある特定のブランチ `{branch}` を指定して複製します。

```bash
$ git clone {remote} -b {branch}
```

リモートリポジトリ `{remote}` にある特定のタグ `{tag}` を指定して複製します。

```bash
$ git clone {remote} -b {tag}
```

ローカルリポジトリにクローン済みのディレクトリ `{cloned-dir}` を、指定したディレクトリ `{dir}` に複製します。

```bash
$ git clone -l -s -n {cloned-dir} {dir}
```

## git pull

### リモートリポジトリの変更内容を取り込む

リモートリポジトリの変更内容を取り込ます。
`git fetch` と `git merge origin/master` を実施しています。

```bash
$ git pull
```

リモートリポジトリの特定ブランチ `{branch}` の変更内容を取り込ます。

```bash
$ git pull origin {branch}
```

## git fetch

### リモートリポジトリの最新を取得する

リモートリポジトリの最新を取得します。

```bash
$ git fetch
$ git fetch origin
```

リモートリポジトリにある特定のブランチ `{branch}` のデータを取得します。

```bash
$ git fetch origin {branch}
```

## git push

### リモートリポジトリに変更内容を反映させる

指定したブランチ `{branch}` の内容を、リモートリポジトリに反映させます。

```bash
$ git push origin {branch}
$ git push origin {branch} -f    # 強制的にプッシュします。※上書きになるため注意が必要。
```

## git branch

### ブランチの一覧を表示する

```bash
$ git branch        # ローカルブランチの一覧を表示する
$ git branch -r     # リモートブランチの一覧を表示する(--remotes)
$ git branch -a     # ローカルブランチとリモートブランチの一覧を表示する(--all)
$ git branch -v     # 詳細情報(先頭のコミットのIDとメッセージ)を付与した状態で、ブランチの一覧を表示する(--verbose)
```

### ブランチを作成する

現在のHEADから、指定した `{branch}` を名前として、新しいブランチを作成します。
新しく作成したブランチへの切り替えは行いません。

```bash
$ git branch {branch}
```

### ブランチを削除する

指定したブランチ `{branch}` を削除します。
指定したブランチの内容が、HEADにマージされていないと削除できません。

```bash
$ git branch -d {branch}
```

マージの状態に関わらず、指定したブランチ `{branch}` を削除します。

```bash
$ git branch -D {branch}
```

### ブランチ名を変更する

現在チェックアウトしているブランチ名を新しいブランチ名 `{new-branch}` に変更します。

```bash
$ git branch -m {new-branch}
```

## git checkout

### ブランチを切り替える

指定したブランチ `{branch}` に切り替えます。

```bash
$ git checkout {branch}
```


現在のブランチをベースに、ブランチ `{branch}` を作成して切り替えます。

```bash
$ git checkout -b {branch}
```

リモートリポジトリの指定したブランチ `origin/{branch}` をベースに、ブランチ `{branch}` を作成して切り替えます。

```bash
$ git checkout -b {branch} origin/{branch}
```

### 特定のコミットに切り替える

指定したコミット（ハッシュ値） `{hash}` に切り替えます。

```bash
$ git checkout {hash}
```

### ローカルの変更を取り消す

特定のファイル `{file}` の変更を取り消します。

```bash
$ git checkout {file}
```

特定のディレクトリ `{dir}` 以下の変更を再起的に取り消します。

```bash
$ git checkout {dir}
```

全てを元に戻します。

```bash
$ git checkout .
```

## git merge

### 変更をマージする

ブランチの変更をmasterへマージします。

```bash
$ git checkout master
$ git merge {branch}
```

masterの変更をブランチへマージします。

```bash
$ git checkout {branch}
$ git merge master
```

### 競合を解消する

以下の順番に競合を解消します。

1. 衝突したファイルを修正します。
1. `$ git add`
1. `$ git commit`

## git rebase

歴史の元を改変します。
ブランチの統合、コミットまとめ、などなど。

### ブランチへ統合する

masterの変更をブランチへ統合します。

```bash
$ git checkout {branch}
$ git rebase master
```

### 直近のコミットをまとめる

以下3つコミットをまとめます。

```bash
$ git log --oneline
5590c80 commit 3
a4641b0 commit 2
1c3cff8 commit 1
...
```

1. `$ git rebase -i HEAD~3`
1. 2つめと3つめのコミットに対して、コマンド `pick` を ``squash (s)`に書き換える（コミットログを変更しない場合は、`fixup (f)` でもOK）。
1. 保存して終了します。

## git status

### ブランチの状態を表示する

ワーキングツリーの状態を表示します。

```bash
$ git status
```

短い書式で表示します。

```bash
$ git status -s
```

## git add

### 変更内容をインデックスに追加する

指定したディレクトリ `{dir}` 以下の変更内容をコミット対象にします。ステージング。

```bash
$ git add {dir}
```

指定したファイル `{file}` の変更内容をコミット対象にします。

```bash
$ git add {file}
```

バージョン管理されているファイルのみコミット対象にします。

```bash
$ git add -u
```

## git reset

### ファイルをインデックスから取り除く

全てのファイルを取り消します。

```bash
$ git reset HEAD
```

指定したファイル `{file}` を取り消します。

```bash
$ git reset HEAD {file}
```

## git commit

### ブランチへコミットする

ブランチへコミットします。

```bash
$ git commit -m "comment"
```

### コミットログを修正する

直前のコミットログを修正します。

```bash
$ git commit --amend -m "comment"
```

## git remote

### リモートリポジトリを確認する

```bash
$ git remote -v
```

## git log

### コミットログを確認する

```bash
$ git log
$ git log {ファイル名}    # 特定ファイルのみ
$ git log --oneline       # 1行表示
$ git log --numstat       # ファイル毎の削除追加行数を表示
$ git log --name-status   # 変更ファイルを表示
$ git log --grep='typo'   # コミットログでgrep
$ git log --merges        # マージコミットのみ
```
