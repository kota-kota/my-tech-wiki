# Dokuwikiに導入したプラグイン

DokuWikiに導入したプラグインをまとめておきます。

## Box

[plugin:box](https://www.dokuwiki.org/plugin:box)

文字を囲む。

## catList

[ja:plugin:catlist](https://www.dokuwiki.org/ja:plugin:catlist)

再帰的にページと名前空間を一覧表示する。

例：オプション指定なし

```text
<catlist>
```

例：いくつかオプション指定あり

```text
<catlist -noAddPageButton -noHead -sortAscending -excluns:"secret|playground" -excluPage:"header">
```

## Changes

[plugin:changes](https://www.dokuwiki.org/plugin:changes)

変更履歴を表示する。

※以下の例は、Jekyllの変換に失敗するため、`{` と `}` のような単一括弧の記載としています。実際に使う場合は、`{` と `}` は2重括弧に読み換えてください。

**例：リスト表示**

```text
{changes>render = list}
```

**例：リスト表示（ユーザ名/時刻表示）**

```text
{changes>render = list(signature)}
```

**例：pagelistプラグイン連携**

```text
{changes>render = pagelist}
```

**例：pagelistプラグイン連携（ヘッダ表示）**

```text
{changes>render = pagelist(header)}
```

## color

[plugin:color](https://www.dokuwiki.org/plugin:color)

文字に色を付ける。

## EditTable

[plugin:edittable](https://www.dokuwiki.org/plugin:edittable)

DokuWikiの表をエクセルのように編集できる。

## ImgPaste

[plugin:imgpaste](https://www.dokuwiki.org/plugin:imgpaste)

DokuWikiの文書作成中に、画像をクリップボードから貼り付けることができる。

**使い方**

- クリップボードにある画像を `Ctrl + V` で張り付けることができます。
- 画像ファイルは、`pasted` フォルダに日時を追加して保存されます。
- 保存されるファイル名は「サイト設定」から変更できます。

## IndexMenu

[plugin:indexmenu](https://www.dokuwiki.org/plugin:indexmenu)

参考サイト  
<https://dokuwiki.oreda.net/plugin/indexmenu.html>

※以下の例は、Jekyllの変換に失敗するため、`{` と `}` のような単一括弧の記載としています。実際に使う場合は、`{` と `}` は2重括弧に読み換えてください。

**使い方の例**

```text
{indexmenu>.|js navbar noscroll notoc tsort nsort skipfile=/sidebar/ skipns=/^(wiki|playground)$/ }
```

- `indexmenu>.`  
  全て開いた状態にします。`.#1` にすると現在のレベルのみを開いた状態になります。
- `js`  
  Windowsエクスプローラ風にして、右クリックをできるようにします。
- `navbar`  
  現在の名前空間を開いた状態にします。
- `noscroll`  
  表示幅に収まらなかった場合にスクロールバーを表示しないようにします。**js**オプション指定時のみ有効です。
- `notoc`  
  TOCを無効にします。**js**オプション指定時のみ有効です。
- `tsort`  
  ページのタイトルでソートします。
- `nsort`  
  名前空間もページソートオプションと同様にソートします。
- `msort`  
  ページごとに埋め込まれたメタデータタグに基づいてソートします。メタデータタグとは `indexmenu_n>10` のような文字列のことです。

**メタデータタグについて**

`indexmenu_n>10` の「10」という部分を任意の数値にすることができ、その数値の昇順によってページが並び替えられます。メタデータタグはwikiが解釈できる範囲内であればページの何処にあってもいいです。

**注意**

2020年6月のバージョンアップ後、「機能フラグ」の「defer_js」をOffにしないと、うまくJavaScriptが表示できません。

## Move

[plugin:move](https://www.dokuwiki.org/plugin:move)

ページ移動・ページ名変更・名前空間変更

## PageList

[ja:plugin:pagelist](https://www.dokuwiki.org/ja%3Aplugin%3Apagelist)

きれいにフォーマットした表として、wikiページの一覧を表示します。

Changesプラグインと一緒に使用しますが、単体での用途が今のところ不明。。

## plantumlparser

[plugin:plantumlparser](https://www.dokuwiki.org/plugin:plantumlparser)

文書内にUML図を作成できます。

**例**

```text
<uml>
actor Actor
card Card
cloud Cloud
component Component
database Database
file File
folder Folder
frame Frame
interface Interface
node Node
package Package
storage Strage
node "Rock64 SMC" {
  frame "ROS" {
    component "Controle node"
  }
}
</uml>
```

## SyntaxHighlighter4

[plugin:syntaxhighlighter4](https://www.dokuwiki.org/plugin:syntaxhighlighter4)

シンタックスハイライト

**例**

```text
<sxh c title:sample.c>
#include <stdio.h>
int main()
{
    int test = 0;
    printf("Hello,world! %d\n", test);
    return 0;
}
</sxh>
```

**カラーテーマを変更する**

「管理」->「サイト設定」から好きなカラーテーマを選択する。

各テーマのスタイルシートは、`config/dokuwiki/lib/plugins/syntaxhighlighter4/dist` にある。

## TocTweak

[plugin:toctweak](https://www.dokuwiki.org/plugin:toctweak)

目次(TOC:Table of Contents)を作成します。

- 特定のページだけ目次を非表示にします。
- デフォルトで非表示にします。
- Wikipeidaのように、ページのインラインに目次を表示します。

※以下の例は、Jekyllの変換に失敗するため、`{` と `}` のような単一括弧の記載としています。実際に使う場合は、`{` と `}` は2重括弧に読み換えてください。

**使い方の例**

```text
{INLINETOC}
```

**スタイルを変更する**

スタイルシートは `lib/plugins/toctweak` にあり、`all.css` を修正する。

以下のような変更を実施してみました。

- 本文とのマージンを広げます。
- 目次のデザイン(h3)を変更します。
- 囲い線をなくします。
- リストをできるだけ左に寄せます。

```css
/*------------------------------------*
   INLINETOC
 *------------------------------------*/
div.toc_inline {
    margin-top: 10px;
    margin-bottom: 30px;   // 変更前：5px
    // border-color: __border__;    // 変更前：有効
    // border-style: solid;    // 変更前：有効
    // border-width: thin;    // 変更前：有効
    padding-top: 10px;
    // border-radius: 10px;    // 変更前：有効
    // background-color: __background_alt__;
  h3 {
    padding-left: none;    // 変更前：2em
    font-size: 1em;    // 変更前：.875em
    font-weight: bold;
    border-bottom: none;    // 変更前：なし（下線あり）
  }
  > div {
    font-size: .9375em;
    // padding-left: 1.6em;    // 変更前：有効
    padding-right: 1.6em;
  }
  ul {
      list-style-type: square;
      line-height: 1.5em;
      li.clear {
          list-style-type: none;
      }
  }
}
[dir=rtl] div.toc_inline {
  h3 {
    padding-right: 1em;    // 変更前：2em
    font-size: 1em;    // 変更前：.875em
    font-weight: bold;
    border-bottom: none;    // 変更前：なし（下線あり）
  }
  ul {
      list-style-type: square;
      line-height: 1.5em;
  }
}
```
