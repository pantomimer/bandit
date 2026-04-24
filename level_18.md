# Bandit Level 18 → Level 19

## 目標

ホームディレクトリの `readme` ファイルにパスワードが書かれている。
ただし `.bashrc` が改ざんされており、SSH でログインすると即座にログアウトされてしまう。

## 使うコマンド

- `ssh`  : サーバーへのリモートログイン（コマンドを直接実行する使い方）

## 手順

### Step 1: .bashrc の中身を確認する

まず何が仕掛けられているか確認する。

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat .bashrc
```

末尾に以下のような記述が見つかる。

```bash
echo 'Byebye !'
exit
```

ログインのたびに `exit` が実行されて即ログアウトされる仕掛けだとわかる。

### Step 2: SSH ログインと同時にコマンドを実行する

通常通りログインしようとすると即座に弾かれる。
SSH にはログイン後に実行するコマンドを直接渡せる機能があるので、それを使う。

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
```

パスワードを入力すると、シェルを起動せずに `cat readme` だけが実行され、
パスワードが表示される。それが **bandit19 のパスワード**。

## ポイント

### なぜ通常ログインできないのか？

SSH でログインすると、シェル（bash）が起動する。
bash 起動時には `.bashrc` というファイルが自動で読み込まれる。

今回は `.bashrc` の末尾に `exit` コマンドが仕込まれており、
ログインと同時に強制ログアウトされる仕掛けになっている。

```
Byebye !    ← .bashrc の exit によって即ログアウト
```

---

### SSH にコマンドを直接渡す

`ssh` コマンドの末尾にコマンドを書くと、
**シェルを起動せずにそのコマンドだけを実行**して接続を終了する。

```
ssh ユーザー@ホスト -p ポート <実行したいコマンド>
```

`.bashrc` はシェル起動時に読み込まれるため、
シェルを起動しないこの方法では `.bashrc` の罠を踏まずに済む。

```bash
# NG: .bashrc が読み込まれて即ログアウト
ssh bandit18@bandit.labs.overthewire.org -p 2220

# OK: シェルを起動せず cat だけ実行
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
```

---

### 他の回避方法

シェルを起動するが `.bashrc` を読み込まない `--norc` オプションを使う方法もある。

```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "bash --norc"
```

これで対話的なシェルに入れるので、その後 `cat readme` を実行してもよい。

## 結果

入手したパスワードを `passwords.txt` の `bandit19` 行に記録する。
