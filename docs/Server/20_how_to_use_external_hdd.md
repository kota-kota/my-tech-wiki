---
title: 外付けHDDを使用する
---

外付けHDDを使用するための手順を記載します。

## 外付けHDDを使用できるようにする

### 外付けHDDを認識させる

外付けHDDをUSBで接続します。

`dmesg` コマンドを使用して、記憶媒体の認識情報とパーティション番号を確認します。
ほとんどの場合、`/dev/sda` や `/dev/sdb` などで認識されるはず。

```bash
dmesg
```

### パーティションを作成する

#### 2TB以下の場合

`fdisk` コマンドを使用して、`/dev/sda` として認識されたHDDに対してパーティションを作成します。

```bash
sudo fdisk /dev/sda
```

以下のような流れで対話形式で作成していきます。

```bash
Command: p                     # 'p'入力。ディスク状態を確認
Command: d                     # 'd'入力。不要なパーティションがあれば削除
Command: n                     # 'n'入力。新たなパーティションを作成
Partition number: 1            # '1'入力。パーティション番号
First sector:                  # 初期値のまま
Last sector:                   # 初期値のまま
Command: w                     # 'w'入力。書き込み
```

#### 2TBより大きい場合

`parted` コマンドを使用して、`/dev/sdb` のパーティションを作成します。

`/dev/sdb` を指定して、`parted` コマンドを実行します。
`(parted)`と表示され、コマンド入力のモードになります。

```bash
sudo parted /dev/sdb
```

ディスクの状態を確認し、パーティションが作られていないことを確認します。

```text
(parted) p
Model: Inateck ASM1153E (scsi)
Disk /dev/sdb: 4001GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Disk Flags:

Number  Start  End  Size  Type  File system  Flags
```

ディスクラベルを作成します。2TBより大きいサイズを扱うため、ディスクラベルはGPTを指定します。
また、単位をテラバイトとして指定する。

```text
(parted) mklabel gpt
Warning: The existing disk label on /dev/sdb will be destroyed and all data on this disk will be lost. Do you want to continue?
Yes/No? Yes
(parted) unit TB
```

ディスクの状態を確認し、`Partition Table` が `gpt` になっていることを確認します。

```text
(parted) p
Model: Inateck ASM1153E (scsi)
Disk /dev/sdb: 4.00TB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags:

Number  Start  End  Size  File system  Name  Flags
```

4TB（任意のサイズ）のパーティションを作成します。

```text
(parted) mkpart primary 0.00TB 4.00TB
```

ディスクの状態を確認し、4TBのパーティションが作成されていることを確認します。

```text
(parted) p
Model: Inateck ASM1153E (scsi)
Disk /dev/sdb: 4.00TB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name     Flags
 1      0.00TB  4.00TB  4.00TB               primary
```

### フォーマットする

パーティションに対して、ext4でフォーマットします。
`/dev/sda` なら `/dev/sda1`、`/dev/sdb` なら `/dev/sdb1` がパーティションになります。

```bash
sudo mkfs.ext4 /dev/sda1
```

### ボリュームラベルを設定する

ext4でフォーマットされたパーティションに対して、ボリュームラベル `<label_name>` を設定します。

```bash
sudo tune2fs -L <label_name> /dev/sda1
```

パーティションのUUIDとボリュームラベルが正しく設定されているか確認します。

```bash
sudo blkid /dev/sda1
```

## マウントの設定

`/etc/fstab` にマウントの設定を記載します。

```text title="/etc/fstab"
LABEL=[ラベル名] [マウントディレクトリ] ext4 defaults 0 0
```

マウントします。

```bash
sudo mount -a
```

## 【失敗】Windowsでマウントする

WindowsのExt2Fsdでマウントできるようにいろいろ試したがダメでした。。。
いろいろ設定いじったので、念のためやったことをメモしておきます。

```bash
# パーティションのチェック
sudo e2fsck -f /dev/sda1
# 64bitオプションを無効にする
sudo resize2fs -s /dev/sda1
# metadata_csumオプションを無効にする
sudo tune2fs -O ^metadata_csum /dev/sda1
```
