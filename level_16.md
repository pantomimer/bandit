# Bandit Level 16 → Level 17

## 目標

localhost のポート **31000〜32000** のどこかに正解のサービスが動いている。
まずポートスキャンで候補を絞り、SSL に対応しているポートに bandit16 のパスワードを送ると、
bandit17 ログイン用の **SSH 秘密鍵**が返ってくる。

## 使うコマンド

- `ssh`    : サーバーへのリモートログイン
- `nmap`   : ポートスキャン（どのポートが開いているか調べる）
- `openssl s_client` : SSL/TLS 暗号化通信で接続する
- `echo`   : 文字列を出力する
- `|`      : パイプ

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit16@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 15 で入手したもの（`passwords.txt` の `bandit16` 行）を使う。

### Step 2: 開いているポートをスキャンする

```bash
nmap -sV localhost -p 31000-32000
```

開いているポートとそのサービス情報が表示される。

```
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
```

`ssl/` がついていて、かつ `echo`（送ったものをそのまま返すだけ）ではないポートが正解。
上の例では **31790** が候補。

### Step 3: 正解のポートにパスワードを送信する

```bash
echo "bandit16のパスワード" | openssl s_client -connect localhost:31790 -quiet
```
echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -connect localhost:31790 -quiet
成功すると SSH 秘密鍵が返ってくる。

```
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmDoB9vbOdwuSMcT...
（省略）
-----END RSA PRIVATE KEY-----
```

### Step 4: 秘密鍵をファイルに保存する

返ってきた秘密鍵（`-----BEGIN RSA PRIVATE KEY-----` から `-----END RSA PRIVATE KEY-----` まで）を
保存する。

```bash
nano bandit17.key
```

内容を貼り付けて保存し、権限を設定する。

```bash
chmod 600 bandit17.key
```

### Step 5: ローカルマシンに秘密鍵を持ち出す

Level 13 と同様に、**ローカルの別ターミナル**で鍵の内容をコピーして保存しておく。

```bash
# サーバー上で確認
cat bandit17.key
```

表示された内容をローカルの `bandit17.key` に保存し、`chmod 600 bandit17.key` を実行する。

## ポイント

### `nmap` とは？

`nmap` は **ネットワーク上のポートをスキャン**するツール。
どのポートが開いていて、どんなサービスが動いているかを調べられる。

| オプション       | 意味                                        |
|------------------|---------------------------------------------|
| `-sV`            | サービスのバージョン情報も取得する          |
| `-p 31000-32000` | スキャン対象のポート範囲を指定する          |

---

### `echo` ポートを除外する理由

`echo` サービスは送ったデータをそのまま返すだけで、パスワードを受け付けない。
`nmap` の結果で `ssl/echo` と出たポートは候補から外す。

```
ssl/echo    → 送ったものが返ってくるだけ（不正解）
ssl/unknown → 正体不明のサービス（これが正解の可能性大）
```

---

### 返ってくるのはパスワードではなく SSH 秘密鍵

Level 13 と同様に、次のレベルへのログインには秘密鍵を使う。
返ってきた鍵を大切に保存し、Level 17 のログインに備える。

## 結果

次のレベルは秘密鍵でログインするため、`passwords.txt` へのパスワード記録は不要。
保存した `bandit17.key` を Level 17 のログインに使う。
