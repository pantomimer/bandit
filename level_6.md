# Bandit Level 6 → Level 7

## 目標

サーバー上のどこかにあるファイルから、以下の条件をすべて満たすものを見つけてパスワードを入手する。

- 所有ユーザーが **bandit7**
- 所有グループが **bandit6**
- サイズが **33 バイト**

## 使うコマンド

- `ssh`  : サーバーへのリモートログイン
- `find` : 条件を指定してファイルを検索する
- `cat`  : ファイルの内容を表示

## 手順

### Step 1: SSH でログイン

```bash
ssh bandit6@bandit.labs.overthewire.org -p 2220
```

パスワードは Level 5 で入手したもの（`passwords.txt` の `bandit6` 行）を使う。

### Step 2: サーバー全体を条件で検索

```bash
find / -type f -size 33c -user bandit7 -group bandit6 2>/dev/null
```

マッチしたファイルのパスが表示される（例: `/var/lib/dpkg/info/bandit7.password`）。

### Step 3: ファイルを読む

```bash
cat /var/lib/dpkg/info/bandit7.password
```

（表示されたパスに合わせて変える）

表示された文字列が **bandit7 のパスワード**。

## ポイント

### Level 5 との違い：検索範囲がサーバー全体

Level 5 は `inhere` ディレクトリ以下だけを検索すればよかった。
今回は検索場所が `/`（ルートディレクトリ＝サーバー全体）になる。

```bash
# Level 5: inhere 以下だけ
find . -type f ...

# Level 6: サーバー全体
find / -type f ...
```

---

### 新しいオプション：所有者・グループで絞り込む

| オプション       | 意味                         |
|------------------|------------------------------|
| `-user bandit7`  | 所有ユーザーが bandit7 のもの |
| `-group bandit6` | 所有グループが bandit6 のもの |

Linux のファイルにはそれぞれ「所有ユーザー」と「所有グループ」が設定されている。
`ll` の出力の3列目と4列目がそれにあたる。

```
-rw-r----- 1 bandit7 bandit6 33 Apr 3 15:17 /var/lib/dpkg/info/bandit7.password
              ^^^^^^^ ^^^^^^^
           所有ユーザー 所有グループ
```

---

### `2>/dev/null` とは何か？

`/` から検索すると、自分がアクセスできないディレクトリに大量に出くわし、
`Permission denied`（権限なし）というエラーが画面に溢れてしまう。

```
find: '/root': Permission denied
find: '/etc/ssl/private': Permission denied
...（何百行も続く）
```

`2>/dev/null` はこれを防ぐための書き方。

| 記号         | 意味                                                 |
|--------------|------------------------------------------------------|
| `2>`         | 標準エラー出力（エラーメッセージの出力先）を切り替える |
| `/dev/null`  | 何を書き込んでもすべて捨てられる「ゴミ箱」           |

つまり「エラーメッセージはゴミ箱に捨てて、結果だけ表示する」という意味になる。

```bash
# エラーが大量に出て結果が見づらい
find / -type f -size 33c -user bandit7 -group bandit6

# エラーを捨てて結果だけ見やすく表示
find / -type f -size 33c -user bandit7 -group bandit6 2>/dev/null
```

## 結果

入手したパスワードを `passwords.txt` の `bandit7` 行に記録する。
