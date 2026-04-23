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

### Step 2: 秘密鍵を確認

```bash
ls
```

`sshkey.private` があることを確認する。

### Step 3: 秘密鍵を使って bandit14 にログイン

同じサーバー内に `localhost`（自分自身）へ SSH する。

```bash
ssh -i sshkey.private bandit14@localhost -p 2220
```

パスワードを聞かれずにそのままログインできる。

### Step 4: bandit14 のパスワードを読む

```bash
cat /etc/bandit_pass/bandit14
```

表示された文字列が **bandit14 のパスワード**。

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

### `localhost` とは？

`localhost` は「今接続しているサーバー自身」を指すホスト名。
つまり bandit13 として接続中のサーバーに、bandit14 として再ログインしている。

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
