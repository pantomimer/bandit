# Bandit Level 15 → Level 16

## 目標

bandit15 のパスワードを **localhost のポート 30001** に **SSL/TLS 暗号化通信**で送信すると、
bandit16 のパスワードが返ってくる。

## 使うコマンド

- `ssh`    : サーバーへのリモートログイン
- `openssl s_client` : SSL/TLS 暗号化通信でサーバーに接続する
- `echo`   : 文字列を出力する
- `|`      : パイプ

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit15@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 14 で入手したもの（`passwords.txt` の `bandit15` 行）を使う。

### Step 2: SSL でパスワードを送信する

```bash
echo "bandit15のパスワード" | openssl s_client -connect localhost:30001 -quiet
```


実際には `passwords.txt` の `bandit15` 行の文字列を入れる。

成功すると証明書情報のあとに次のパスワードが返ってくる。

```
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

これが **bandit16 のパスワード**。

## ポイント

### Level 14 との違い：なぜ `nc` ではダメなのか？

Level 14 は平文（暗号化なし）の通信だったので `nc` で接続できた。
Level 15 のポート 30001 は **SSL/TLS で暗号化された通信しか受け付けない**。
`nc` は暗号化に対応していないため、`openssl s_client` を使う必要がある。

```bash
# NG: 暗号化なしのため接続できない
echo "パスワード" | nc localhost 30001

# OK: SSL/TLS で暗号化して接続
echo "パスワード" | openssl s_client -connect localhost:30001 -quiet
```

---

### SSL/TLS とは？

**SSL/TLS** はネットワーク通信を暗号化する仕組み。
Web サイトの `https://` がまさにこれを使っている。

- 通信内容を暗号化して盗聴を防ぐ
- 接続先が正規のサーバーであることを証明する

---

### `openssl s_client` のオプション

| オプション              | 意味                                             |
|-------------------------|--------------------------------------------------|
| `-connect localhost:30001` | 接続先のホストとポートを指定                  |
| `-quiet`                | 証明書などの詳細情報を省略してすっきり表示する   |

`-quiet` を省くと証明書情報が大量に表示されるが、パスワードは同じく受け取れる。

---

### `verify error` が出ても気にしない

接続時に以下のようなメッセージが出ることがあるが、今回は無視してよい。

```
verify error:num=18:self-signed certificate
```

これは「自己署名証明書（公式機関が保証していない証明書）」を使っているという警告で、
Bandit のような学習用サーバーでは一般的。パスワードの送受信には影響しない。

## 結果

入手したパスワードを `passwords.txt` の `bandit16` 行に記録する。
