# GitHubの認証に個人アクセストークンとSSHキーを使用する

今までGitHubへのアクセスにはパスワード認証を使ってきましたが、GitHubがパスワード認証を廃止するというお知らせがメールで届きました。
2021年8月13日にパスワード認証が廃止されるようです。  
<https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/>

そのため、パスワード認証の変わりに個人アクセストークンとSSHキーを使用するようにします。

## 個人アクセストークン

以下のドキュメントを参考にします。  
<https://docs.github.com/ja/github/authenticating-to-github/creating-a-personal-access-token>

### 個人アクセストークンを取得する

- GitHubにログインします。
- 右上のプロフィール画像をクリックして、`Settings` をクリックします。
- `Developer settings` をクリックします。
- `Personal access tokens` をクリックします。
- `Generate new token` をクリックします。
- Noteにトークンのわかりやすい名前を記入します。
  リポジトリアクセス用なので、「Access to my repo」としました。
- 権限を設定します。
  トークンを使用してコマンドラインからリポジトリにアクセスするため、`repo` 関連を選択します。
  - repo
  - admin:repo_hook
  - delete_repo
- `Generate token` をクリックします。
- トークンをクリップボードにコピーします。
  ページを離れると参照できなくなるため、パスワードのように管理が必要です。

### 個人アクセストークンを使用する

HTTPS経由でGitの操作をする際に、パスワードの代わりにそのトークンを入力できます。

例えば、コマンドラインでは以下のように入力できます。

```bash
$ git clone https://github.com/username/repo.git
Username: my_username
Password: my_token
```

## SSHキー

以下のドキュメントを参考にします。  
<https://docs.github.com/ja/github/authenticating-to-github/connecting-to-github-with-ssh>

### SSHキーを取得する

SSHキーを生成します。
Windowsの場合は、GitBashで生成できます。

```bash
$ ssh-keygen -t rsa -b 4096   # 全てデフォルトでエンター連打
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Kyohei/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:

$ ls
id_rsa  id_rsa.pub
```

### SSHキーをGitHubアカウントへ追加する

- GitHubにログインします。
- 右上のプロフィール画像をクリックして、`Settings` をクリックします。
- `SSH and GPG keys` をクリックします。
- `New SSH key` をクリックします。
- Keyに `id_rsa.pub` の内容を記入します。
- `Add SSH Key` をクリックします。

### SSHキーを使用する

コマンドラインからクローンする例です。
SSHキー認証されるため、パスワード要求はされません。

```bash
$ git clone git@github.com:kota-kota/kota-kota.github.io.git
Cloning into 'kota-kota.github.io'...
The authenticity of host 'github.com (52.69.186.44)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,52.69.186.44' (RSA) to the list of known hosts.
remote: Enumerating objects: 413, done.
remote: Counting objects: 100% (413/413), done.
remote: Compressing objects: 100% (336/336), done.
remote: Total 413 (delta 108), reused 363 (delta 61), pack-reused 0
Receiving objects: 100% (413/413), 437.53 KiB | 718.00 KiB/s, done.
Resolving deltas: 100% (108/108), done.
```

SSHキー生成の時にpassphraseを設定することでパスワードを要求させることもできます。
