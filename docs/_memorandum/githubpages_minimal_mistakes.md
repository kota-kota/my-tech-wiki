# GitHub Pages + Minimal Mistakes を導入する

個人ブログとして Jekyll と Minimal Mistakes テーマをGitHub Pagesに導入してみたので、そのメモです。
以下ができることを望んでいました。

- Markdown入力に対応している
- Markdownファイルを直接扱うことができる。
  - 好きなエディタで編集できる。
  - 差分管理ができる。
- カテゴリやタグで文書管理ができる。

## Minimal Mistakes テーマ

Jekyll テーマは数多く存在し、探せばいくらでも出てくるが、どんなデザインがいいかよくわかりませんでした。
いくつかおすすめのテーマを探してるうちに、「Minimal Mistakes」がよさそうとのことだったので、今回はこのテーマを使ってみることにしました。

## Minimal Mistakes テーマを GitHub Pages で使う

### GitHub Pages でホームページを作る

GitHub Pages については公式ドキュメントがあります。

- GitHub公式のドキュメンテーション  
  <https://docs.github.com/ja/pages>

GiHubでホームページを公開するには、ホームページ用のリポジトリを作成する必要があります。
ユーザの場合は、`{username}.github.io` のリポジトリ名で作成しなければいけません。

ホームページ用のリポジトリから、「Settings」→「Pages」でGitHub Pagesに飛べます。
ここで、「Your site is published at ...」と緑枠で表示されていればOKです。

### Minimal Mistakes テーマをローカル環境で使ってみる

Minimal Mistakes テーマの導方法は公式サイトに記載があります。

- Quick-Start Guide  
  <https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/>

まずはローカルに環境構築して、ローカル環境で動作することを確認します。

#### Ruby + Jekyllをインストールする

まず、rubyをインストールします。Windowsの場合はruby on windowsです。

<https://rubyinstaller.org/>  
rubyinstaller-devkit-3.0.1-1-x64.exe

インストーラーを起動して、一直線に進めていきます。
下図のようなコマンドプロンプトが出たら、1,2,3を指定します。

![2021-06-15-start_githubpages_minimal_mistakes-01](/assets/images/2021-06-15-start_githubpages_minimal_mistakes-01.png)

インストールが完了したら、rubyコマンドとgemコマンドが動作するか確認します。

```bash
$ ruby -v
ruby 3.0.1p64 (2021-04-05 revision 0fb782ee38) [x64-mingw32]

$ gem -v
3.2.15
```

次に、jekyllをインストールします。gemコマンドを使います。

```bash
$ gem install jekyll bundler
```

インストールが完了したら、jekyllコマンドが動作するか確認します。

```bash
$ jekyll -version
jekyll 4.2.0
```

#### Minimal Mistakes テーマをインストールする

リポジトリをクローンします。  
<https://github.com/mmistakes/minimal-mistakes>

Quick-Start Guide の Remove the Unnecessary に記載のあるファイルを全て削除します。

```text
.editorconfig
.gitattributes
.github
/docs
/test
CHANGELOG.md
minimal-mistakes-jekyll.gemspec
README.md
screenshot-layouts.png
screenshot.png
```

#### Minimal Mistakes テーマを使う

Gemfileを以下のページに従って変更します。  
<https://mmistakes.github.io/minimal-mistakes/docs/installation/#install-dependencies>

```ruby
gem "github-pages", group: :jekyll_plugins
```

はコメントアウトを外して有効にします。

`_config.yml` の `theme` と `remote_theme` のコメントアウトを外して有効にします。

以下のコマンドを実行します。

```bash
$ bundle install
$ bundle exec jekyll serve
```

エラーが出ました。

```text
Please add the following to your Gemfile to avoid polling for changes:
gem 'wdm', '>= 0.1.0' if Gem.win_platform?
```

以下をGemfileに追記します。

```ruby
gem 'wdm', '>= 0.1.0' if Gem.win_platform?
```

`$ bundle update` コマンドをたたきます。

またエラーが出ました。

```text
C:/install/Ruby30-x64/lib/ruby/gems/3.0.0/gems/jekyll-3.9.0/lib/jekyll/commands/serve/servlet.rb:3:in `require': cannot load such file -- webrick (LoadError)
```

以下のコメントを実行します。
このコマンドによって、Gemfileにwebrickが追記され、インストールされます。

```bash
$ bundle add webrick
```

成功すると、以下のような感じになります。

```bash
$ bundle exec jekyll serve
Configuration file: C:/workspace/minimal-mistakes/_config.yml
            Source: C:/workspace/minimal-mistakes
       Destination: C:/workspace/minimal-mistakes/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
      Remote Theme: Using theme mmistakes/minimal-mistakes
       Jekyll Feed: Generating feed for posts
   GitHub Metadata: No GitHub API authentication could be found. Some fields may be missing or have incorrect data.
   GitHub Metadata: No internet connection. GitHub metadata may be missing or incorrect.
                    done in 12.73 seconds.
 Auto-regeneration: enabled for 'C:/workspace/minimal-mistakes'
    Server address: http://127.0.0.1:4000
  Server running... press ctrl-c to stop.
```

ブラウザで <http://127.0.0.1:4000> にアクセスしてページが表示されることを確認します。

### Minimal Mistakes テーマをGitHub Pagesで使う

ローカル環境で動作確認したファイル群を、ホームページ用のリポジトリにプッシュするだけです。

プッシュしたあと、「Page build successfully」メールが届きます。
失敗したら「Page build failure」メールが届きます。

ブラウザで <https://username.github.io/> にアクセスして、ローカルと同じページが表示されたらOKです。

「Page build warning」が届きました。以下のような内容でした。

```text
You are attempting to use a Jekyll theme, "minimal-mistakes-jekyll", which is not supported by GitHub Pages.
```

ググったら以下が引っ掛かりました。  
<https://github.com/mmistakes/minimal-mistakes/issues/1394>

どうも、`_config.yml` で、`theme` プロパティにGitHub Pagesでサポートしていない "minimal-mistakes-jekyll" を指定したからみたいです。
`remote_theme` プロパティで問題なく動くためwarningということですね。
`_config.yml` で `theme` プロパティをコメントアウトすることで解決しました。

### レイアウトの設定

以下のサイトを参考にしました。  
<https://k11i.biz/blog/2016/08/11/starting-jekyll-with-minimal-mistakes/>  
<https://www.mk-mode.com/blog/2019/01/27/jekyll-with-minimal-mistakes/>

- _config.ymlを修正した。
- タグ別一覧を出すようにした。
- カテゴリ別一覧を出すようにした

#### Markdownの見出し1をページタイトルとして使う

Jekyllにおけるページタイトルは、YAML Front Matterの `page.title` を投稿に埋め込むことで表示されます。そのため、Markdownの見出し1（#)を使うと2重でタイトルが表示されてしまいます。以下のようなイメージ。

```text
---
title: Test Page
---

# Test Page
```

タイトルはMarkdownの見出しをそのまま出したいため、`jekyll-titles-from-headings` プラグインを導入します。

このことについて、GitHubのissueでも投稿があったため参考にしました。  
<https://github.com/mmistakes/minimal-mistakes/issues/1618>

このプラグインの導入方法については以下を参考にしました。  
<https://talk.jekyllrb.com/t/title-derived-from-heading-appears-twice-on-top-of-page/1686/4?u=mmistakes>

#### 内部マークダウンファイルへのリンクを使用する

内部マークダウンのリンクの書き方は以下のようになるが、これだと Jekyll(GitHub Pages) では見れない。

```md
[Foo](foo.md)
```

Jekyll(GitHub Pages) のリンクの書き方は以下のようになるが、これだとマークダウンエディタのプレビューでは見れない。（`\%` は `%` に読み換えてください。`%` のままだとJekyllがリンクだと勘違いしてビルドエラーになったため）

```md
[Foo]({\% link foo.md \%})
```

内部マークダウンのリンクの書き方のまま、Jekyll(GitHub Pages) で見れるようにしたいため `jekyll-relative-links` プラグインを導入します。

`Gemfile` に以下を追記します。

```ruby
gem "jekyll-relative-links"
```

`_config.yml` に以下を追記します。`relative_link:` を記載しないと上手く変換してくれませんでした。

```yml
plugins:
  - jekyll-relative-links

whitelist:
  - jekyll-relative-links

relative_links:
  enabled:     true
  collections: true
```

#### 最近の投稿ページのレイアウトを変更する

##### 1ページに表示にする最大投稿数を変える

`_config.yml` の `paginate` を変更します。

```yaml
# Outputting
paginate: 5 # amount of posts to show
```

##### 投稿日を表示する

`_config.yml` の `defaults:` に `show_date: true` を追記します。

```yaml
# Defaults
defaults:
  # _posts
  - scope:
      path: ""
      type: posts
    values:
      show_date: true
```

##### 投稿毎に1行目が表示されるのを非表示する

`_includes/archive-single.html` を変更します。

以下がその内容の抜粋で、`if post.excerpt` の行が投稿毎の1行目の内容を表示している行なので、ここを削除すれば非表示になります。

`post.excerpt` に投稿毎の1行目が格納されているみたい。

##### タイトルのサイズを変更する

`_includes/archive-single.html` を変更します。

以下がその内容の抜粋で、`h2 class="archive__item-title no_toc" itemprop="headline"` がタイトルになります。これの `h2` を変更すればいいです。
