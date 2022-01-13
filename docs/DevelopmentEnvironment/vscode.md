# Visual Studio Code

## インストール

テキストエディタ。

以下のサイトからインストーラをダウンロードしてインストールするだけです。

<https://code.visualstudio.com/download>

マルチプラットフォームで使えるエディタですが、Windowsで使います。
Linux開発は、拡張機能 `Remote Development` を使ってリモートで開発します。

環境変数 `PATH` を通して、コマンド `code` を使えるようにしておきます。

```bash
> code -v
1.41.1
26076a4de974ead31f97692a0d32f90d735645c0
x64
```

## 拡張機能ジェネレータ

`npm` コマンドを使って、拡張機能のジェネレータをインストールします。

```bash
> npm install -g yo generator-code --save-dev
```

ジェネレータを使って、拡張機能のひな形を作成します。
対話形式でプロジェクトを作成していきます。

```bash
> npx yo code
```

パッケージ `.vsix` ファイルを作成するために、`vsce` をインストールします。

```bash
> npm install -g vsce --save-dev
```

パッケージを作成します。

```bash
> vsce package
```

パッケージを作成するためには最低限以下を実施する必要があります。

- `package.json` に `publisher` と `repogitry` を追記します。
- `README.md` を修正します。

## 拡張機能：本体

### Japanese Language Pack for Visual Studio Code

日本語化する。

### Open in Editor

VSCodeで開いているファイルを外部エディタで開く。

- **外部エディタを設定する**

`settings.json` に外部エディタの実行ファイルのパスを設定する。
以下はサクラエディタを設定した例。

```json
{
    "alt-editor.binary": "C:\\Program Files (x86)\\sakura\\sakura.exe",
    "alt-editor.args": "{filename} -X={column} -Y={line} -VY={line}",
}
```

- **外部エディタでファイルを開く**

`Alt+Shift+E` で、現在開いているファイルを外部エディタで開くことができる。

### Path Autocomplete

パスの入力を支援してくれる。

- **拡張子を含める**

```json
{
    "path-autocomplete.extensionOnImport": true
}
```

### Remote Development

VSCodeからリモート環境へ接続し、開発することができる。

- **WSL環境へ接続する**

コマンドパレットから `Remote-WSL: NewWindow` を選択し、WSLに接続します。

SSHクライアント側（VSCode側）で、公開鍵を生成します。

```bash
$ ssh-keygen
```

SSHクライアント側（VSCode側）で、コンフィグファイル `${User}/.ssh/config` に接続先を以下のように記述します。

```text
Host host1
    HostName 192.168.109.132    #接続先IPアドレス
    Port 22                     #接続先ポート番号
    User kyohei                 #ユーザ名
    IdentityFile ~/.ssh/id_rsa  #公開鍵
```

SSHサーバ側（WSL側）で、SSHサーバパッケージをインストールしておく。

```bash
$ sudo apt-get install openssh-server
```

SSHサーバ側（WSL側）に、クライアント側の公開鍵を登録します。
`authorized_keys` ファイルに公開鍵の内容をコピーします。

```bash
$ cd ~/.ssh
$ touch authorized_keys
$ vim authorized_keys
```

VSCodeのRemote-SSHから接続します。

### Sort JSON objects

jsonファイルをキーでソートします。

- **キーをアルファベット順にソートする**

ソートしたいjsonファイルを開き、コマンドパレットから `Sort JSON` を実行する。

> **注意**  
> 間違ったjsonの記法だと正しくソートされません。
> 例えば、以下のようにリストの最後にカンマがあるとダメです。
>
> ```json
> "vsnotes.templates": [
>     "tags",
> ],
> ```

### vscode-icons

Visual Studio Codeのファイルアイコンを変える。

### VSNotes

Markdown形式のテキストメモの作成やタグ管理できる。

- **初期設定**

コマンドパレットから `VSNotes: Run setup` を実行し、ノートを保存するフォルダを設定します。

- **新しいノートを作成する**

コマンドパレットから `VSNotes: Create a New Note` を実行し、テンプレートを選択します。
ファイル名を入力すれば新しいノートが作成されます。

- **デフォルトのファイル名を変える**

デフォルトのファイル名は `日付時刻_タイトル.md` であるが、日付時刻の表示が `2020-02-16_13-00` などのように長い。
時間まではいらない気がするので、`20200216` のようにシンプルにする。

```json
"vsnotes.tokens": [
    {
        "type": "datetime",
        "token": "{dt}",
        "format": "YYYYMMDD",
        "description": "Insert formatted datetime."
    },
    {
        "type": "title",
        "token": "{title}",
        "description": "Insert note title from input box.",
        "format": "Untitled"
    },
    {
        "type": "extension",
        "token": "{ext}",
        "description": "Insert file extension.",
        "format": "md"
    }
],
```

- **テンプレートを作る**

タグをいちいちつけるのは面倒だし、忘れがちなので、以下のようなテンプレートとして準備をしておく。

ファイル -> 基本設定 -> ユーザースニペット をクリック。

検索窓から `markdown.json` を開き、以下を追記する。

```json
"vsnote_template_tags": {
"prefix": "vsnote_template_tags",
"body": [
    "---",
    "tags:",
    "1. Notes",
    "---",
    "\n",
    "# Title",
    "\n",
],
"description": "Template with Tags",
}
```

`settings.json` に以下を追記する。

```json
"vsnotes.templates": [
    "tags",
],
```

> - `markdown.json` は、Visual Studio Codeのスニペット機能です。使える変数は以下を参照すること。
>   <https://code.visualstudio.com/docs/editor/userdefinedsnippets#_variables>
> - 同じように `markdown.json` にスニペットを定義して、`settings.json` の `vsnotes.templates` に指定すれば複数のテンプレート登録が可能です。

## 拡張機能：Git

### GitLens

`git blame` を便利に使えるようにする。

GitLens画面を開くと、ブランチ一覧やコミット一覧が見れます。
コミットを開くと、そのコミットで変更のあったファイルの一覧が表示されます。
ファイルを選択すると、上の画像のように差分が見えます。

個人的にはコード内で行をマウスオーバーすると、その行を変更したコミットが出てくるのが一番便利だと思う。

## 拡張機能：Markdown

Visual Studio Code を使ってMarkdownのテキスト文書を作成します。

### Markdown All in One

Markdownファイルの編集に便利な機能を提供する。
「ショートカットキー」「自動でプレビュー表示する」「アウトライン解析」「TableOfContents」などなど。

### Markdown Preview Github Styling

プレビュー時のスタイルをGithub風にする。
Markdown Preview Enhancedではなく通常のプレビューで反映される。

スタイルシートは `~\.vscode\extensions\bierner.markdown-preview-github-styles-0.1.6\github-markdown.css` にある。

### markdownlint

MarkdownのLinting機能を提供する。

### Paste Image

Markdown形式のテキストファイルに、クリップボードにある画像を直接貼り付ける。

画像を貼り付けるには、

- コマンドパレットから `Paste Image` を実行する。
- ショートカットキー `Ctrl+Alt+V` で貼り付ける。

のいずれかを実施すればいいです。

- **画像を保存するパスを設定する**

`path` を指定することで設定できます。

```json
{
    "pasteImage.path": "${projectRoot}/assets/images"
}
```

- **画像のファイル名の前に文字を付ける**

`namePrefix` を指定することで設定できます。

```json
{
    "pasteImage.namePrefix": "${currentFileNameWithoutExt}-",
}
```

- **ペースト時に画像のファイル名を変更する**

`showFilePathConfirmInputBox` を `true` にします。

`filePathConfirmInputBoxMode` で、変更対象をファイル名だけにするかパスまで含めるか選択できます。

```json
{
    "pasteImage.filePathConfirmInputBoxMode": "onlyName",
    "pasteImage.showFilePathConfirmInputBox": true
}
```

- **ペースト時にテキストに挿入するパスを変更する**

`insertPattern` を指定することで設定できます。

```json
{
    "pasteImage.insertPattern": "${imageSyntaxPrefix}/assets/images/${imageFileName}${imageSyntaxSuffix}",
}
```

### TextTables

Markdownにおけるテーブル（表）データの編集をサポートする。

1. Markdownファイルを開く。
1. `Ctrl+Q` を2回押して、“Table Mode”をONにする。

## 拡張機能：PlantUML

### PlantUML

plantUMLを使えるようにする。

PlantUMLを実行するためには、Javaの実行環境が必要となります。
<https://java.com/ja/> からインストーラをダウンロードして、インストールします。

PlantUMLでUMLを描画するためには、Graphvizが必要となります。
<https://www.graphviz.org/> からインストーラをダウンロードして、インストールします。

Graphvizを使用できるようにするために、環境変数 `GRAPHVIZ_DOT` にdot.exeのパスを指定します。
もしくは、`settings.json` に以下のように記述してもいいです。

```json
{
    "plantuml.commandArgs": [
        "-DGRAPHVIZ_DOT=${インストールしたパス}/graphviz-2.38/release/bin/dot.exe",
    ],
}
```

## 拡張機能：C/C++

### C/C++ for Visual Studio Code

C/C++のインテリセンスおよびデバッグの拡張機能を提供する。

### CMake

CMakeの言語サポート。

## 拡張機能：Python

### Python extension for Visual Studio Code

Pythonの拡張機能を提供する。

- **パッケージをインストールする**

```bash
$ python -m pip install --upgrade pip   # pipをアップグレード
$ python -m pip install {package}       # インストール
```

## 拡張機能：JavaScript

JavaScriptの実行環境である `Node.js` をインストールします。
以下のサイトからインストーラをダウンロードしてインストールするだけです。

<https://nodejs.org/en/download/>

環境変数 `PATH` を通して、コマンド `node` と `npm` を使えるようにしておきます。

```bash
> node -v
v14.15.4
> npm -v
6.14.10
```

Visual Studio Codeでデバッグ実行します。

- `F5` を押下します。
- 「環境の選択」で、`Node.js` を選択します（初回のみ）。
- `node.exe {.jsファイル}` が実行されます。

## 拡張機能：TypeScript

以下のサイトが参考になった。  
<https://maku.blog/p/ak7u3h3>

TypeScriptのプロジェクトを作成します。
> `npm install` で TypeScript をインストールするときに、`-g` オプションを付けるとシステムグローバルにインストールすることができますが、開発環境の依存を防ぐために、TypeScript はプロジェクトローカルにインストールしておいた方がよい。

```bash
> npm init -y   # package.json を作成
> npm install typescript --save-dev   # tscコマンドをインストール
> npm install @types/node --save-dev  # @types/node パッケージをインストール
> npx tsc -v
Version 4.4.3
> tsc -v
Version 4.4.3
> ts-node -v
v10.2.1
> npx tsc --init # tsconfig.json を作成
```

Visual Studio Codeでビルド実行します。
`Ctrl+Shift+B` を押下し、`tsc: build - tsconfig.json` を選択します。

## 拡張機能：HTML

### Live Server

簡易ローカルサーバを立てることができる拡張機能。
ファイルを更新すると自動的にブラウザを更新する。

VSCodeの右下に表示される `Go Live` を押下すると、ブラウザで <http://127.0.0.1:5500/> が実行されます。
コマンドパレットから「Live Server: Open with Live Server」を選択することで実行することもできます。

## 拡張機能：Rust

The Rust Programming Language 日本語版 を見れば全てが分かる。  
<https://doc.rust-jp.rs/book-ja/>

コマンドラインツール `rustup` をインストールします。  
<https://www.rust-lang.org/ja/tools/install>

`rustup` を使って、関連ツールをインストールします。

> `rls` は、`Rust Language Server` のことで、自動補完、定義へのジャンプ、インラインのエラー表示などの機能を持ちます。

```bash
> rustup component add rust-analysis
> rustup component add rust-src
> rustup component add rls
> rustup component add rustfmt  # 自動フォーマッタ
```

### Rust

## 拡張機能：Go

Goをインストールします。  
<https://golang.org/dl/>

コマンド `go` が使えるか確認します。

```bash
> go version
go version go1.17.2 windows/amd64
```

### Go

コマンドパレットから `Go: Install/Update Tools` を実行し、以下のパッケージをインストールします。

- gopkgs
- go-outline
- gopls
