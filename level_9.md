# Bandit Level 9 → Level 10

## 目標

`data.txt` はバイナリファイル。その中にある人間が読める文字列のうち、
`=` 記号が複数並んでいる行の隣にパスワードが書かれている。

## 使うコマンド

- `ssh`     : サーバーへのリモートログイン
- `strings` : バイナリファイルから人間が読める文字列を抽出する
- `grep`    : キーワードを含む行を抽出する
- `|`       : パイプ（コマンドの出力を次のコマンドに渡す）

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit9@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 8 で入手したもの（`passwords.txt` の `bandit9` 行）を使う。

### Step 2: 人間が読める文字列を抽出して `==` を含む行を探す

```bash
strings data.txt | grep ==
```

`==========` に続く行がいくつか表示される。

```
 ========== the
========== password
========== is
========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

つなげて読むと "the password is FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey" となる。
最後の行の `==========` の右側が **bandit10 のパスワード**。

## ポイント

### `cat` で開いてはいけない理由

`data.txt` はバイナリファイルなので `cat` で開くと文字化けした内容が大量に流れ、
ターミナルの表示が壊れることもある。

```bash
# NG: 文字化けして何も読めない（ターミナルが壊れることも）
cat data.txt
```

---

### `strings` コマンドとは？

バイナリファイルの中から「人間が読める文字列（4文字以上の連続した印字可能文字）」
だけを抜き出すコマンド。

```bash
strings data.txt
```

バイナリデータを除いたテキスト部分だけが表示される。

---

### `strings` と `grep` をパイプでつなぐ

`strings` の出力はまだ多いので、`grep` でさらに `=` を含む行だけに絞り込む。

```
strings data.txt | grep =
     ↑                  ↑
読める文字列を抽出   = を含む行だけ表示
```

---

### ターミナルが壊れてしまったときの対処

うっかり `cat data.txt` を実行してターミナルの表示がおかしくなった場合は、
以下のコマンドで元に戻せる。

```bash
reset
```

## 結果

入手したパスワードを `passwords.txt` の `bandit10` 行に記録する。
