---
title: Mkdocsをカスタマイズする
---

## 参考サイト

- mkdocs の設定など、デザインとか参考にさせてもらった  
    <https://fereria.github.io/reincarnation_tech/10_Programming/99_Documentation/00_mkdocs_setting/>

- mkdocs の設定など、参考にできるのが多そう  
    <https://dev.classmethod.jp/articles/mkdocs-and-material-for-mkdocs-tips-matome/>

- Material for MkDocs 設定項目一覧  
    <https://squidfunk.github.io/mkdocs-material/setup/changing-the-colors/>

- Material for MkDocs リファレンス一覧  
    <https://squidfunk.github.io/mkdocs-material/reference/abbreviations/>

## フッターの内容を編集する

カスタムディレクトリを有効にする。
カスタムディレクトリ配下に置いたhtmlファイルが上書きされる。

```yaml title="mkdocs.yml"
theme:
  custom_dir: 'custom_dir'
```

Material for MkDocsのフッターボディは以下にある。  
<https://github.com/squidfunk/mkdocs-material/blob/master/material/partials/footer.html>

フッターボディを編集したい場合は、上記ファイルを `./custom_dir/partials/footer.html` に配置して編集する。

フッターを削除する場合は、空の `./custom_dir/partials/footer.html` を作成すればいい。

## コードハイライトを有効にする

`pymdownx.highlight` を有効にする。

```yaml title="mkdocs.yml"
markdown_extensions:
  - pymdownx.highlight:
      use_pygments: true
      noclasses: true
      pygments_style: colorful
      linenums: true
```

`pygments_style` には以下にあるものを指定できる。  
<https://github.com/pygments/pygments/tree/master/pygments/styles>

## サイドメニューのフォルダのタイトルを変える

`mkdocs-awesome-pages-plugin` をインストールする。

```bash
pip install mkdocs-awesome-pages-plugin
```

`mkdocs.yml` に以下を追記する。

```yaml title="mkdocs.yml"
plugins:
  - awesome-pages
```

タイトルを変えたいフォルダ直下に、`.pages` というファイルを作り、以下のようにタイトルを設定する。

```text title=".pages"
title : タイトル
```

すると、フォルダのタイトル名が `.pages` で設定したものになる。
