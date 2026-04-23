# Bandit Level 13 → Level 14

## 目標

ホームディレクトリに SSH 秘密鍵ファイル `sshkey.private` がある。
パスワードは渡されないので、この鍵を使って bandit14 としてログインし、
`/etc/bandit_pass/bandit14` からパスワードを読み取る。

## 使うコマンド

- `ssh`  : サーバーへのリモートログイン
- `-i`   : SSH 鍵ファイルを指定するオプション
- `cat`  : ファイルの内容を表示

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit13@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 12 で入手したもの（`passwords.txt` の `bandit13` 行）を使う。

### Step 2: 秘密鍵の内容をコピーする

```bash
cat sshkey.private
```

表示された内容（`-----BEGIN OPENSSH PRIVATE KEY-----` から始まる文字列）を
**すべてコピー**しておく。

### Step 3: ローカルマシンに鍵ファイルを作る

bandit13 のセッションは一旦そのままにして、**ローカルの別ターミナル**を開く。

```bash
# ローカルのターミナルで実行
vi bandit14.key
```

コピーした内容を貼り付けて保存する（`Ctrl+X` → `Y` → `Enter`）。

続いて、秘密鍵ファイルに適切な権限を設定する（これをしないと SSH が鍵を拒否する）。

```bash
chmod 600 bandit14.key
```

### Step 4: ローカルから bandit14 に直接ログイン

```bash
ssh -i bandit14.key bandit14@bandit.labs.overthewire.org -p 2220
```

### Step 5: パスワードファイルを確認

```bash
cat /etc/bandit_pass/bandit14
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
```


## ポイント

### SSH 鍵認証とは？

通常の SSH ログインはパスワードで本人確認を行う。
**SSH 鍵認証**はパスワードの代わりに「鍵のペア」で本人確認する方式。

| 鍵の種類 | 役割                                         |
|----------|----------------------------------------------|
| 秘密鍵   | 自分だけが持つ。絶対に他人に渡さない         |
| 公開鍵   | サーバーに登録する。誰に見られても問題ない   |

ログイン時にサーバーは「この秘密鍵に対応する公開鍵が登録されているか？」を確認する。
一致すればパスワードなしでログインできる。

---

### `-i` オプションとは？

`-i` は "identity file"（身元証明ファイル）の意味。
使う秘密鍵ファイルを明示的に指定するオプション。

```bash
ssh -i sshkey.private bandit14@localhost -p 2220
     ↑
   使う秘密鍵ファイルを指定
```

---

### なぜ localhost が使えないのか？

Bandit サーバーは負荷対策のため、サーバー自身（localhost）からの SSH 接続をブロックしている。
そのため、一度ローカルマシンに秘密鍵を持ち出して、外から bandit14 に接続する必要がある。

---

### `chmod 600` が必要な理由

SSH は秘密鍵ファイルの権限が緩すぎると、セキュリティ上の理由でエラーを出して拒否する。

```
Permissions 0644 for 'bandit14.key' are too open.
```

`chmod 600` で「自分だけ読み書きできる」権限に設定することで解消できる。

---

### `/etc/bandit_pass/` とは？

Bandit では各レベルのパスワードが `/etc/bandit_pass/banditN` に保存されている。
ただしそれぞれ対応するユーザーしか読めない権限になっている。

```bash
# bandit14 としてログイン後なら読める
cat /etc/bandit_pass/bandit14
```

## 結果

入手したパスワードを `passwords.txt` の `bandit14` 行に記録する。
