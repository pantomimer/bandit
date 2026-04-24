# Bandit Level 19 → Level 20

## 目標

ホームディレクトリにある `bandit20-do` という特殊なバイナリを使って、
bandit20 の権限でコマンドを実行し、パスワードを入手する。

## 使うコマンド

- `ssh`  : サーバーへのリモートログイン
- `ll`   : ファイルの詳細一覧（パーミッションを確認する）
- `./bandit20-do` : setuid バイナリの実行

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit19@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 18 で入手したもの（`passwords.txt` の `bandit19` 行）を使う。

### Step 2: バイナリのパーミッションを確認する

```bash
ll
```

以下のように表示される。

```
-rwsr-xr-x 1 bandit20 bandit19 14876 Apr  3 15:17 bandit20-do
```

所有者が `bandit20` で、パーミッションに `s` が含まれているのがポイント。

### Step 3: バイナリを使って bandit20 の権限でコマンドを実行する

```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

bandit20 しか読めないはずのパスワードファイルが表示される。
それが **bandit20 のパスワード**。

## ポイント

### setuid とは？

通常、コマンドを実行すると「実行した人」の権限で動く。
**setuid** が設定されたバイナリは、「ファイルの所有者」の権限で動く。

```
通常:    実行したユーザー（bandit19）の権限で動く
setuid:  ファイルの所有者（bandit20）の権限で動く
```

つまり `bandit20-do` を実行すると、自分が bandit19 であっても
bandit20 として各種コマンドを実行できる。

---

### パーミッションの `s` を読む

`ll` の出力でパーミッションを確認する。

```
-rwsr-xr-x 1 bandit20 bandit19 ...
   ↑
   s = setuid が設定されている
```

通常の実行権限 `x` の位置に `s` が表示されていたら setuid が有効。

| パーミッション | 意味                                     |
|----------------|------------------------------------------|
| `rws`（所有者）| 読み取り・書き込み・setuid 実行が可能    |
| `r-x`（グループ）| 読み取り・実行のみ可能                 |
| `r-x`（その他）| 読み取り・実行のみ可能                   |

---

### `/etc/bandit_pass/bandit20` が読める理由

```bash
ll /etc/bandit_pass/bandit20
# -r-------- 1 bandit20 bandit20 33 ... bandit20
```

このファイルは bandit20 しか読めない。
しかし `bandit20-do` 経由で実行すると bandit20 の権限になるため読み取れる。

## 結果

入手したパスワードを `passwords.txt` の `bandit20` 行に記録する。
