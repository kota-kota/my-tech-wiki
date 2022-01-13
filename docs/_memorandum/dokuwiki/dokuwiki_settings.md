# Dokuwikiの各種設定をまとめる

DokuWikiにおける各種設定項目についてまとめておきます。

## サイトの設定

DokuWikiの「管理」->「サイト設定」でカスタマイズした内容を記載します。

ここで変更した内容は、`config/dokuwiki/conf/local.php` に保存されます。
このファイルを編集することで変更することもできます。

### DokuWiki

#### 基本

| 設定値                             | 項目    | 備考                                         |
| ---------------------------------- | ------- | -------------------------------------------- |
| Wikiタイトル（あなたのWikiの名前） | My Wiki | 悩む。。                                     |
| テンプレート（Wikiのデザイン）     | arctic  | arcticテンプレートをインストールして設定する |

#### 表示

| 設定値                                        | 項目                 | 備考                                                             |
| --------------------------------------------- | -------------------- | ---------------------------------------------------------------- |
| トレース（パンくず）表示数（0で無効化します） | 0                    | トレースいらない。現在位置だけでいい                             |
| 現在位置を表示                                | チェック             | 現在位置は欲しい                                                 |
| 最終編集者の情報として表示する内容            | ユーザーのフルネーム | ログイン名は極力出さないように                                   |
| 目次のトップレベル見出し                      | 1                    |                                                                  |
| 目次を生成する最小見出し数                    | 2                    |                                                                  |
| 目次に表示する最大レベルの見出し              | 3                    |                                                                  |
| 部分編集を有効にする最大レベルの見出し        | 0                    | ページのセクション毎の編集釦を消す                               |
| 最初の見出しをページ名とする                  | 常に使用する         | 現在位置やトレースには、見出しが表示されているほうが分かりやすい |

#### 認証

| 設定値                      | 項目       | 備考                         |
| --------------------------- | ---------- | ---------------------------- |
| DokuWiki の動作を無効にする | ユーザ登録 | 他にも要らないものあると思う |

#### 編集

| 設定値                   | 項目     | 備考 |
| ------------------------ | -------- | ---- |
| HTML埋め込みを許可する   | チェック |      |
| PHP埋め込みを許可する    | チェック |      |
| キャッシュ保持時間（秒） | 1        |      |

#### リンク

| 設定値                     | 項目   | 備考 |
| -------------------------- | ------ | ---- |
| 外部リンクのtarget属性     | _blank |      |
| メディアリンクのtarget属性 | _blank |      |
| Windowsリンクのtarget属性  | _blank |      |

#### 機能フラグ

| 設定値                                                 | 項目           | 備考                          |
| ------------------------------------------------------ | -------------- | ----------------------------- |
| ページのHTMLが解析されるまでJavascriptの実行を延期する | チェックを外す | IndexMenuプラグイン動作のため |

### プラグイン

#### IndexMenu

| 設定値                                                          | 項目     | 備考                                                               |
| --------------------------------------------------------------- | -------- | ------------------------------------------------------------------ |
| 管理者にはページの注釈の先頭に indexmenu ソート番号を表示する。 | チェック | ソート番号が表示されるが管理者でログインしているときだけなので許容 |

## デザインの設定

DokuWikiのデザイン設定を記載します。

### arcticテンプレート

#### スタイルのカスタマイズ

`lib/tpl/arctic` にスタイルシート群があります。
ここに自分専用のスタイルシート `my_style.css` を作成します。
そして、これを反映させるために `style.ini` に以下のように追記する。

```ini
arctic_layout.css = screen
arctic_design.css = screen
arctic_media.css  = screen
arctic_rtl.css    = rtl
arctic_print.css  = print

my_style.css      = screen   # 追加
```

あとは `my_style.css` を好きなように修正すればいいです。
以下は少し手を加えてみた一例です。

```css
/* my headline setup */
div.dokuwiki h1 {
    font-size: 200%;
        margin-bottom: 0.5em;
    padding-bottom: 10px;
}
div.dokuwiki h2 {
    font-size: 180%;
        margin-top: 2em;
        margin-bottom: 0.5em;
    padding-top: 5px;
    padding-bottom: 10px;
    padding-left: 5px;
    border-left: 10px solid __border__;
}
div.dokuwiki h3 {
        margin-top: 1em;
        margin-bottom: 0.5em;
    padding-top: 5px;
    padding-bottom: 5px;
    padding-left: 5px;
    border-left: 10px solid __border__;
}
div.dokuwiki h4 {
        margin-top: 1em;
        margin-bottom: 0.5em;
    padding-top: 5px;
    padding-bottom: 5px;
    padding-left: 5px;
    border-left: 10px double __border__;
}
div.dokuwiki h5 {
        margin-top: 1em;
        margin-bottom: 0.5em;
}

/* my toc */
div.dokuwiki #dw__toc {
    width: 350px;
}

/* my code */
div.dokuwiki code {
    background-color: __lightgray__;
    padding: 2px;
    border-radius: 0.3em;
}
```

### DokuWiki標準テンプレート

#### テンプレートのスタイル設定

「管理」->「テンプレートのスタイル設定」でカスタマイズした内容を記載します。

| 設定項目     | デフォルト | 変更後 |
| ------------ | ---------- | ------ |
| サイトの全体 | 75em       | 100%   |

ここでの変更は、`conf/tpl/dokuwiki/style.ini` ファイルに保存されます。

#### スタイルのカスタマイズ

参考にしたサイト  
<https://dokuwiki.oreda.net/template/dokuwiki.html>

`lib/tpl/dokuwiki` にスタイルシート群があります。
ここに自分専用のスタイルシート `css/my_style.css` を作成します。
そして、これを反映させるために `style.ini` に以下のように追記する。

```ini
（略）
css/_edit.css             = screen
css/_modal.css            = screen
css/_forms.css            = screen
css/_admin.css            = screen
css/structure.less        = screen
css/design.less           = screen
css/pagetools.less        = screen
css/content.less          = screen
css/my_style.css          = screen  # ここ
```

あとは `css/my_style.css` を好きなように修正すればいいです。
以下は少し手を加えてみた一例です。

```css
body {
    min-width: 1150px;
}
 
h1 {
    color: #666;
    font-size: 1.8em;
    margin: 0 0 0.444em;
}
h2 {
    color: white;
    font-size: 1.4em;
    margin: 0 0 0.5em;
    font-weight: normal;
    background-color: #557697;
    padding: 14px 0px 12px 10px;
}
h3 {
    color: #666;
    font-size: 1.2em;
    margin: 0 0 0.888em;
    font-weight: normal;
    padding: 5px 0px 0px 5px;
    background-color: white;
    border-left: solid 10px #557697;
    border-top: none;
    border-bottom: 3px solid #557697;
    border-right: none;
}
h4 {
    color: #666;
    font-size: 1.0em;
    margin: 0 0 1.0em;
    padding: 6px 6px 6px 6px;
    font-weight: normal;
    background-color: white;
    border-left: solid 8px #557697;
    border-top: none;
    border-bottom: none;
    border-right: none;
}
h5 {
    font-size: .8em;
    margin: 0 0 1.1428em;
    padding: 6px 6px 6px 6px;
    /*border-bottom: 3px solid #557697;*/
    border-bottom: none;
    border-left: 8px solid #557697;
    font-weight: normal;
}
h6 {
    font-size: .75em;
    margin: 0 0 1.333em;
    border-bottom: dotted 1px #cacaca;
    font-weight: normal;
}
```

#### サイドバーを付ける

`sidebar` という名前のページを作ると、そのページがサイドバーになります。

## その他の設定

### デフォルトページを削除する

DokuWikiには、以下の4つのデフォルトページが存在します。

- wiki:welcome（ウェルカムページ）
- wiki:dokuwiki（DokuWikiの説明ページ）
- wiki:syntax（整形記法・構文一覧）
- playground:playground（練習用ページ）

「**wiki:welcome**」と「**wiki:dokuwiki**」については、不要なので削除します。

「**wiki:syntax**」と「**playground:playground**」については削除しても問題ありませんが、ページ編集画面にリンク箇所があるのでページを削除した場合リンク切れとなります。このリンク切れを解消したい場合は、`inc/lang/ja/edit.txt` を以下のように修正します。

```text
編集して`保存`をクリックしてください。Wikiの構文については [[doku>ja:wiki:syntax]] を参考にしてください

当然のことですが、この文書の質を **向上** させる場合のみ編集してください。もし編集方法や構文を練習したいのであれば [[doku>playground:playground]] を利用してください。
```

InterWikiリンクでリンク先をDokuWiki公式サイトのページへと変更しています。
