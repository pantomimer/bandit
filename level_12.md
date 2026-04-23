# Bandit Level 12 → Level 13

## 目標

`data.txt` は16進数ダンプ（hexdump）されたファイル。
元のバイナリに戻すと、gzip・bzip2・tar が何重にも重なった圧縮ファイルになっている。
すべて解凍してパスワードを取り出す。

## 使うコマンド

- `ssh`    : サーバーへのリモートログイン
- `mkdir`  : ディレクトリを作成する
- `cp`     : ファイルをコピーする
- `xxd`    : 16進数ダンプ・逆変換を行う
- `file`   : ファイルの種類を調べる
- `mv`     : ファイル名を変更する
- `gzip`   : gzip 形式の圧縮・解凍
- `bzip2`  : bzip2 形式の圧縮・解凍
- `tar`    : tar アーカイブの作成・展開

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit12@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 11 で入手したもの（`passwords.txt` の `bandit12` 行）を使う。

### Step 2: 作業用ディレクトリを作る

ホームディレクトリには書き込み権限がないため、`/tmp` に作業場所を作る。

```bash
mkdir /tmp/work12
cp data.txt /tmp/work12/
cd /tmp/work12
```

### Step 3: 16進数ダンプを元のバイナリに戻す

```bash
xxd -r data.txt > data.bin
```

`xxd -r` は hexdump を逆変換してバイナリデータに戻す。

### Step 4〜: file で確認 → 解凍 を繰り返す

ここからは `file` でファイルの種類を確認し、種類に応じたコマンドで解凍する。
これを ASCII テキストが出るまで繰り返す。

---

**種類が `gzip` のとき**

```bash
mv ファイル名 ファイル名.gz
gzip -d ファイル名.gz
```

---

**種類が `bzip2` のとき**

```bash
mv ファイル名 ファイル名.bz2
bzip2 -d ファイル名.bz2
```

---

**種類が `tar` のとき**

```bash
tar xf ファイル名
```

展開されたファイル名を `ls` で確認して、次の `file` コマンドに進む。

---

### 作業の流れ（例）

```bash
file data.bin          # → gzip compressed
mv data.bin data.gz
gzip -d data.gz

file data              # → bzip2 compressed
mv data data.bz2
bzip2 -d data.bz2

file data              # → gzip compressed
mv data data.gz
gzip -d data.gz

file data              # → POSIX tar archive
tar xf data

file data5.bin         # → POSIX tar archive
tar xf data5.bin

file data6.bin             # → bzip2 compressed
mv data6.bin data6.bi.bz2
bzip2 -d data6.bz2

...（ASCII text が出るまで繰り返す）

file dataXX            # → ASCII text  ← ゴール！
cat dataXX
```

`The password is ...` という行が表示されたら完了。

## ポイント

### なぜ拡張子を変更する必要があるのか？

`gzip` や `bzip2` は拡張子（`.gz`・`.bz2`）でファイルの種類を判断する。
拡張子がないと解凍を拒否されるため、`mv` で正しい拡張子に変えてから解凍する。

```bash
# NG: 拡張子がないと怒られる
gzip -d data

# OK: 拡張子をつけてから解凍
mv data data.gz
gzip -d data.gz
```

---

### 解凍後のファイル名を確認する習慣

`gzip` や `bzip2` は解凍後に元のファイルを自動削除する。
`tar` は展開後に別ファイルが生成される。
解凍のたびに `ls` で何が残っているか確認しながら進めること。

```bash
gzip -d data.gz   # data.gz が消えて data が生成される
ls                # 確認
file data         # 次の種類を確認
```

---

### 各コマンドのオプション早見表

| コマンド      | 意味              |
|---------------|-------------------|
| `xxd -r`      | hexdump → バイナリ |
| `gzip -d`     | gzip 解凍         |
| `bzip2 -d`    | bzip2 解凍        |
| `tar xf`      | tar 展開（`x`=展開、`f`=ファイル指定） |

## 結果

入手したパスワードを `passwords.txt` の `bandit13` 行に記録する。
