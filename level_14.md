# Bandit Level 14 → Level 15

## 目標

bandit14 のパスワードを **localhost のポート 30000** に送信すると、
bandit15 のパスワードが返ってくる。

## 使うコマンド

- `ssh`  : サーバーへのリモートログイン
- `nc`   : ネットワーク経由でデータを送受信する（netcat）
- `echo` : 文字列を出力する
- `|`    : パイプ

## 手順

### Step 1: SSH でログイン

Level 13 でローカルに保存した秘密鍵を使ってログインする（パスワード不要）。

```bash
ssh -i bandit14.key bandit14@bandit.labs.overthewire.org -p 2220
```

### Step 2: bandit14 のパスワードをポート 30000 に送信する

```bash
echo "bandit14のパスワード" | nc localhost 30000
```

実際には `passwords.txt` の `bandit14` 行の文字列を入れる。

```bash
echo "MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS" | nc localhost 30000
```

成功すると次のパスワードが返ってくる。

```
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

これが **bandit15 のパスワード**。

## ポイント

### `nc`（netcat）とは？

`nc` は **ネットワーク越しにデータを送受信する**コマンド。
「ネットワークの cat」と呼ばれるほどシンプルで、多用途に使える。

```
nc <ホスト名> <ポート番号>
```

今回は `localhost`（自分自身）の `30000` 番ポートに接続している。

---

### ポートとは？

コンピューターは 1 台で複数のサービスを同時に動かせる。
**ポート番号**はサービスを識別するための番号。

| ポート番号 | 一般的な用途                  |
|------------|-------------------------------|
| 22         | SSH                           |
| 80         | HTTP（Web）                   |
| 443        | HTTPS（暗号化 Web）           |
| 30000      | 今回の Bandit 用サービス      |

---

### パイプで自動送信する

`nc` は接続後にキーボードからの入力を待つ。
`echo` とパイプを組み合わせると、入力を自動で送れる。

```bash
# 手動で入力する場合
nc localhost 30000
# （接続後に自分でパスワードを入力して Enter）

# echo でパスワードを自動送信する場合（こちらが楽）
echo "パスワード" | nc localhost 30000
```

---

### 失敗した場合のメッセージ

間違ったパスワードを送ると `Wrong!` と返ってくる。

```
Wrong! Please enter the correct current password.
```

その場合は `passwords.txt` の `bandit14` 行を確認して正しいパスワードを送る。

## 結果

入手したパスワードを `passwords.txt` の `bandit15` 行に記録する。
