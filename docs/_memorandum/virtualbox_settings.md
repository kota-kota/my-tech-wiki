# VirtualBoxの設定まとめ

VirtualBoxの各種設定をまとめて記載します。

## Guest Additions を導入する

Guest Additionsを導入すると、「シームレスなマウス操作」「共有フォルダ」「デスクップ解像度の変更」「シームレスなウィンドウサイズの変更」「ホストとの時刻の同期」「クリップボードの共有」「オートログオン」が使えるようになります。

> <span style="color: red;">**注意**  
> Ubuntu serverでは有効になりません。おそらくウィンドウマネージャが起動しないからだと思われます。</span>

ゲストOSがUbuntuの場合に、Guest Additionsを導入する手順を記載します。

### 事前準備

Guest Additionsのインストールに必要な以下のパッケージをインストールします。

- カーネルヘッダ linux-headers-$(uname -r)
- gcc
- make
- perl

```bash
$ sudo apt update
$ sudo apt install linux-headers-$(uname -r) gcc make perl
```

### インストール

1. VirtualBoxで「デバイス」→「Guest Additions CDイメージの挿入」を選択します。
1. "VBox_GAs_6.1.16" contains software intended to be automatically started. Would you link to run it?" の画面が出てくるので「Run」を選択します。
   - <span style="color: red;">画面が自動で出てこない場合は手動マウントを実行します（次節を参照）。</span>
   - 必要なパッケージがインストールされていないと、「Please install ****」という文言が出て終了するため、もし出てきたら指定されたパッケージをインストールします。
1. 問題なく終了したら、再起動します。

#### Guest Additionsイメージを手動マウントして実行する

手動でマウントします。

```bash
$ mkdir -p /mnt/cdrom
$ mount -r /dev/cdrom /mnt/cdrom
```

インストールスクリプトを実行します。

```bash
$ ./VBoxLinuxAdditions.run
```

## クリップボードの共有

- VirtualBoxで「設定」→「一般」→「高度」タブに移動します。
- クリップボードの共有とドラッグ&ドロップの設定を「双方向」にします。

## ホストオンリーなネットワークアダプターを追加

- VirtualBoxで「設定」→「ネットワーク」に移動します。
- アダプター2の「ネットワークアダプターを有効化」にチェックを入れて、割り当てを「ホストオンリーアダプター」にします。

## ホストネットワークマネージャーの編集

ホストネットワークアダプタの追加・削除・編集ができます。「Host-Only Ethernet Adapter」の設定はここでできます。

入り方は、「ファイル」→「ホストネットワークマネージャー」です。

<span style="color: red;">ホストオンリーアダプタを固定IP設定する場合に。ここで設定されたIPアドレスの範囲が必要になります。</span>
