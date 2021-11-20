---
title: mkdocs を設定する
---

## mkdocs の設定

### インストール

python の `pip` コマンドを使用して mkdocs をインストールする。
mkdocs-material は material テーマを使用するために同時にインストールします。

```bash
pip install mkdocs mkdocs-material
```

### サイトの新規作成

`mkdocs` コマンドを使用してサイトを作成する。

```bash
mkdocs new {my-site}
```

md から html を生成できるか確認するには、ビルドを実行します。

```bash
mkdocs build
```

### ローカルで確認

修正しながらローカルで確認したい場合は、以下のコマンドを実行することで、コンソールでサーバーが起動します。

```bash
mkdocs serve
```

ブラウザで <http://127.0.0.1:8000> にアクセスして確認できます。
編集したら、随時、ページ側も更新されます。

### デプロイ

ビルドした結果を `gh-pages` ブランチへコミットし、リモートへプッシュする。

```bash
mkdocs gh-deploy
```

## mkdocs.yml

### コードハイライト

`pymdownx.highlight` を有効にする。

```yaml
markdown_extensions:
  - pymdownx.highlight:
      use_pygments: true
      noclasses: true
      pygments_style: colorful
      linenums: true
```

`pygments_style` には以下にあるものを指定できる。  
<https://github.com/pygments/pygments/tree/master/pygments/styles>
