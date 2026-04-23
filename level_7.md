# Bandit Level 7 → Level 8

## 目標

`data.txt` の中に大量の単語とパスワードの組み合わせが書かれている。
`millionth` という単語の隣にあるパスワードを見つける。

## 使うコマンド

- `ssh`  : サーバーへのリモートログイン
- `grep` : ファイルの中からキーワードを含む行を検索する

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit7@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 6 で入手したもの（`passwords.txt` の `bandit7` 行）を使う。

### Step 2: data.txt の中から目的の行を探す

```bash
grep millionth data.txt
```

`millionth` を含む行が1行だけ表示される。

```
millionth	dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

右側の文字列が **bandit8 のパスワード**。

## ポイント

### `cat` では対応できない理由

`data.txt` は数万行に及ぶ巨大なファイル。`cat data.txt` で全部表示しても
目的の行を目視で探すのは不可能に近い。

```bash
# NG: 大量の行が流れて目的の行が見つからない
cat data.txt
```

---

### `grep` コマンドとは？

`grep` はファイルの中からキーワードを含む行だけを抜き出すコマンド。

```
grep <検索キーワード> <ファイル名>
```

キーワードが含まれる行だけを表示し、それ以外は無視する。

```bash
# "millionth" を含む行だけ表示する
grep millionth data.txt
```

---

### `grep` はLinux で最もよく使うコマンドのひとつ

ログファイルやコード、設定ファイルなど、膨大なテキストから必要な行を
素早く見つけるために日常的に使う。覚えておいて損はない。

| よく使うオプション | 意味                                   |
|--------------------|----------------------------------------|
| `-i`               | 大文字・小文字を区別しない             |
| `-n`               | 行番号を一緒に表示する                 |
| `-r`               | ディレクトリ以下を再帰的に検索する     |

## 結果

入手したパスワードを `passwords.txt` の `bandit8` 行に記録する。
