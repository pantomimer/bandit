# Bandit Level 4 → Level 5

## 目標

`inhere` ディレクトリにある `-file00` 〜 `-file09` の 10 個のファイルの中から、
人間が読めるファイル（ASCII テキスト）を見つけてパスワードを入手する。

## 使うコマンド

- `ssh`  : サーバーへのリモートログイン
- `ll`   : ファイルの詳細一覧
- `cd`   : ディレクトリの移動
- `file` : ファイルの種類を調べる
- `cat`  : ファイルの内容を表示

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit4@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 3 で入手したもの（`passwords.txt` の `bandit4` 行）を使う。

### Step 2: inhere ディレクトリに移動

```bash
cd inhere/
ll
```

`-file00` 〜 `-file09` の 10 個のファイルが並んでいる。

### Step 3: 全ファイルの種類を一括調査

```bash
file ./-file*
```

`*` はワイルドカードで「`-file` で始まる全ファイル」にマッチする。
出力例：

```
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
```

`ASCII text` と表示されているファイルが人間が読めるファイル。

### Step 4: 該当ファイルを読む

```bash
cat ./-file07
```

（`ASCII text` だったファイル名に合わせて変える）

表示された文字列が **bandit5 のパスワード**。

## ポイント

### `file` コマンドとは？

`file` コマンドはファイルの中身を調べて、その種類を教えてくれるコマンド。
拡張子がなくても、中身のデータを見て判断してくれる。

| 表示される種類   | 意味                               |
|------------------|------------------------------------|
| `ASCII text`     | 人間が読めるテキストファイル       |
| `data`           | バイナリデータ（読んでも文字化け） |
| `JPEG image data`| 画像ファイル                       |

---

### ワイルドカード `*` の使い方

`*` は「任意の文字列」を意味する特殊文字。`-file*` と書くと
`-file00`・`-file01`・・・`-file09` をまとめて指定できる。

```bash
# 1ファイルずつ調べる（非効率）
file ./-file00
file ./-file01
...

# ワイルドカードで一括指定（効率的）
file ./-file*
```

---

### なぜ `./` が必要か？

Level 1 と同じ理由。ファイル名が `-` で始まるため、
`./` をつけてファイルパスとして明示しないとオプションとして誤解釈される。

```bash
# NG: -file* がオプション扱いされエラーになる
file -file*

# OK: パスとして認識される
file ./-file*
```

## 結果

入手したパスワードを `passwords.txt` の `bandit5` 行に記録する。
