# Bandit Level 0 → Level 1

## 目標

SSH でサーバーに接続し、ホームディレクトリにある `readme` ファイルを読んで
Level 1 のパスワードを入手する。

## 使うコマンド

- `ssh` : サーバーへのリモートログイン
- `ls`  : ファイル一覧の確認
- `cat` : ファイルの内容を表示

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

パスワードを聞かれたら `bandit0` と入力する（Level 0 の初期パスワードは公開されている）。

### Step 2: ホームディレクトリを確認

```bash
ls
```

`readme` というファイルがあることを確認する。

### Step 3: readme を読む

```bash
cat readme
```

表示された文字列が **bandit1 のパスワード**。

## ポイント

- `-p 2220` は通常の SSH ポート(22)ではなく 2220 番を指定するオプション
- Bandit では毎回このポートを使う
- パスワードを入力してもターミナルには何も表示されない（セキュリティ仕様）

## 結果

- 入手したパスワード → `passwords.txt` の `bandit1` 行に記録する
