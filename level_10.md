# Bandit Level 10 → Level 11

## 目標

`data.txt` には Base64 でエンコードされたデータが入っている。
デコードしてパスワードを取り出す。

## 使うコマンド

- `ssh`    : サーバーへのリモートログイン
- `cat`    : ファイルの内容を表示
- `base64` : Base64 のエンコード・デコードを行う

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit10@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 9 で入手したもの（`passwords.txt` の `bandit10` 行）を使う。

### Step 2: ファイルの内容を確認

```bash
cat data.txt
```

英数字と `=` だけで構成された1行が表示される。これが Base64 エンコードされたデータ。

```
VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaQ3JJZTNnNldFM1dYOU1nVFM0elVUd==
```

### Step 3: Base64 デコードする

```bash
base64 -d data.txt
```

デコードされた文字列が表示される。

```
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

これが **bandit11 のパスワード**。

## ポイント

### Base64 とは？

Base64 は、バイナリデータや特殊文字を含むデータを、
**英数字と一部の記号（`+`・`/`・`=`）だけで表現する**エンコード方式。

メールの添付ファイルや Web の認証情報など、テキストしか通れない場所に
データを載せるためによく使われる。

```
元のデータ  →（エンコード）→ VGhlIHBhc3N3b3Jk...
            ←（デコード） ←
```

---

### `base64` コマンドの使い方

| コマンド           | 意味                         |
|--------------------|------------------------------|
| `base64 ファイル`  | ファイルを Base64 エンコード |
| `base64 -d ファイル` | Base64 デコード（`-d` = decode） |

---

### Base64 の見分け方

Base64 エンコードされたデータには特徴がある。

- 英数字（`A-Z`・`a-z`・`0-9`）と `+`・`/` だけで構成される
- 末尾に `=` や `==` がつくことがある（パディング）
- 長さが 4 の倍数になっている

`cat data.txt` を実行してこのような文字列が見えたら、Base64 を疑う。

## 結果

入手したパスワードを `passwords.txt` の `bandit11` 行に記録する。
