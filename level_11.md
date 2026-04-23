# Bandit Level 11 → Level 12

## 目標

`data.txt` の内容はすべての英字が **13 文字ずらされて（ROT13）** 書かれている。
元に戻してパスワードを取り出す。

## 使うコマンド

- `ssh` : サーバーへのリモートログイン
- `cat` : ファイルの内容を表示
- `tr`  : 文字を別の文字に置き換える
- `|`   : パイプ（コマンドの出力を次のコマンドに渡す）

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit11@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 10 で入手したもの（`passwords.txt` の `bandit11` 行）を使う。

### Step 2: ファイルの内容を確認

```bash
cat data.txt
```

文字がずれた状態のテキストが表示される。

```
Gur cnffjbeq vf 7k16JArUVv5LxVuJfsSVdbbtaHGlw9D4
```

### Step 3: ROT13 を元に戻す

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

元のテキストが表示される。

```
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```

これが **bandit12 のパスワード**。

## ポイント

### ROT13 とは？

アルファベットを **13 文字分ずらす**暗号。A → N、B → O、... Z → M のように変換する。

```
元: A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
変: N O P Q R S T U V W X Y Z A B C D E F G H I J K L M
```

アルファベットは 26 文字なので、13 ずらして 2 回適用すると元に戻る。
つまり **暗号化と復号に同じ操作を使える**のが特徴。

---

### `tr` コマンドとは？

`tr`（translate）は文字を別の文字に **1対1で置き換える**コマンド。

```
tr '置き換え前の文字の集合' '置き換え後の文字の集合'
```

今回の指定：

| 引数          | 意味                                        |
|---------------|---------------------------------------------|
| `'A-Za-z'`    | 大文字 A〜Z と小文字 a〜z（全アルファベット）|
| `'N-ZA-Mn-za-m'` | それぞれを 13 文字ずらした対応文字に置換  |

具体的には以下の対応になっている。

```
A-M  →  N-Z   （前半 13 文字を後半 13 文字へ）
N-Z  →  A-M   （後半 13 文字を前半 13 文字へ）
a-m  →  n-z   （小文字も同様）
n-z  →  a-m
```

---

### ROT13 は数字・記号には影響しない

ROT13 は英字だけを変換する。数字や記号はそのまま残る。

```
Gur cnffjbeq vf 7k16...
                ^^^^
              数字はそのまま
```

## 結果

入手したパスワードを `passwords.txt` の `bandit12` 行に記録する。
