---
title: Rock64で外付けHDDを使用する
tags:
  - Rock64
weight: 11
type: docs
---

Rock64で外付けHDDを使用するための手順を記載します。

## 外付けHDDを使用できるようにする

### 外付けHDDを認識させる

外付けHDDをUSBで接続します。

`dmesg` コマンドを使用して、記憶媒体の認識情報とパーティション番号を確認します。
ほとんどの場合、`/dev/sda` や `/dev/sdb` などで認識されるはず。

```bash
$ dmesg
```

### パーティションを作成する

#### 2TB以下の場合

`fdisk` コマンドを使用して、`/dev/sda` として認識されたHDDに対してパーティションを作成します。

```bash
$ sudo fdisk /dev/sda
Command: p                     # 'p'入力。ディスク状態を確認
Command: d                     # 'd'入力。不要なパーティションがあれば削除
Command: n                     # 'n'入力。新たなパーティションを作成
Partition number: 1            # '1'入力。パーティション番号
First sector:                  # 初期値のまま
Last sector:                   # 初期値のまま
Command: w                     # 'w'入力。書き込み
```

#### 2TBより大きい場合

`parted` コマンドを使用して、`/dev/sdb` のパーティションを作成する。

`/dev/sdb` を指定して、`parted` コマンドを実行する。

```bash
$ sudo parted /dev/sdb
GNU Parted 3.2
Using /dev/sdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) p
Model: Inateck ASM1153E (scsi)
Disk /dev/sdb: 4001GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Disk Flags:

Number  Start  End  Size  Type  File system  Flags
```

ディスクラベルをGPTとして作成する。

```bash
(parted) mklabel gpt
Warning: The existing disk label on /dev/sdb will be destroyed and all data on this disk will be lost. Do you want to continue?
Yes/No? Yes
(parted) p
Model: Inateck ASM1153E (scsi)
Disk /dev/sdb: 4001GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags:

Number  Start  End  Size  File system  Name  Flags
```

単位をテラバイトとして指定する。

```bash
(parted) unit TB
(parted) p
Model: Inateck ASM1153E (scsi)
Disk /dev/sdb: 4.00TB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags:

Number  Start  End  Size  File system  Name  Flags
```

4TBのパーティションを作成する。

```bash
(parted) mkpart primary 0.00TB 4.00TB
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

パーティション `/dev/sda1` に対して、ext4でフォーマットします。

```bash
$ sudo mkfs.ext4 /dev/sda1
```

### ボリュームラベルを設定する

ext4でフォーマットされたパーティション `/dev/sda1` に対して、ボリュームラベル `<label_name>` を設定します。

```bash
$ sudo tune2fs -L <label_name> /dev/sda1
```

パーティション `/dev/sda1` のUUID,ボリュームラベルが正しく設定されているか確認します。

```bash
$ sudo blkid /dev/sda1
```

## マウントの設定

`/etc/fstab` にマウントの設定を記載します。

```text
LABEL=[ラベル名] [マウントディレクトリ] ext4 defaults 0 0
```

## 番外編

WindowsのExt2Fsdでマウントできるようにいろいろ試したがダメでした。。。

いろいろ設定いじったので、念のためやったことをメモしておきます。

```bash
$ sudo e2fsck -f /dev/sda1      # パーティションのチェック
$ sudo resize2fs -s /dev/sda1   # 64bitオプションを無効にする
$ sudo tune2fs -O ^metadata_csum /dev/sda1   # metadata_csumオプションを無効にする
```
