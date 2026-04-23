# Bandit Level 17 → Level 18

## 目標

ホームディレクトリに `passwords.old` と `passwords.new` の2ファイルがある。
`passwords.new` の中で **1行だけ変更された行**がパスワード。

## 使うコマンド

- `ssh`  : サーバーへのリモートログイン
- `diff` : 2つのファイルの差分を表示する

## 手順

### Step 1: SSH でログイン

Level 16 で保存した秘密鍵を使う（パスワード不要）。

```bash
ssh -i bandit17.key bandit17@bandit.labs.overthewire.org -p 2220
```

### Step 2: 2つのファイルの差分を確認する

```bash
diff passwords.old passwords.new
```

差分が表示される。

```
42c42
< 390zFj2NETFVZkqYw8UEFdN6h40oGVtT
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

`>` から始まる行（`passwords.new` 側）が **bandit18 のパスワード**。

## ポイント

### `diff` の出力の読み方

```
42c42
< 390zFj2NETFVZkqYw8UEFdN6h40oGVtT
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

| 記号 | 意味                                  |
|------|---------------------------------------|
| `<`  | 左側のファイル（`passwords.old`）の行 |
| `>`  | 右側のファイル（`passwords.new`）の行 |
| `---`| 左右の区切り線                        |

`42c42` は「42行目が変更（change）された」という意味。

---

### `diff` コマンドの基本

```
diff <ファイルA> <ファイルB>
```

2つのファイルを比較して、異なる部分だけを表示する。
同じ行は表示されないので、大量の行があっても差分だけを素早く確認できる。

---

### 差分がない場合は何も表示されない

```bash
diff passwords.old passwords.old  # 同じファイル同士
# （何も表示されない）
```

出力がなければ2つのファイルは完全に一致している。

## 結果

入手したパスワードを `passwords.txt` の `bandit18` 行に記録する。
