# Git 超入門ハンズオン

テンプレートから作った自分の `codespaces-env` リポジトリを使って、Git の基本操作を手を動かしながら覚えるための資料です。

> 前提
> - [README](../../README.md) の「使い始め方」に沿って、自分のリポジトリと Codespace を作ってある
> - コマンドはすべて Codespace のターミナルで実行する（ターミナルが見当たらなければ、画面左上のメニュー（☰）→「ターミナル」→「新しいターミナル」で開く）


## 0. まず覚える言葉

| 言葉 | ざっくり意味 |
| --- | --- |
| リポジトリ | ファイルと変更履歴をまとめて管理する箱。このフォルダ全体がそれ |
| コミット | 「ここまでの変更」をセーブすること。セーブポイント |
| ステージ（add） | 次のコミットに含めるファイルを選ぶこと |
| ブランチ | 作業用の分かれ道。`main` を壊さずに試せる |
| プッシュ（push） | 手元のコミットを GitHub に送ること |
| プル（pull） | GitHub の最新の状態を手元に取り込むこと |

流れはこれだけです。

```
ファイルを編集 → git add（選ぶ） → git commit（セーブ） → git push（GitHub に送る）
```


## 1. 今の状態を見る

```bash
cd /workspaces/codespaces-env
git status
```

`nothing to commit, working tree clean` と出れば、変更がないきれいな状態です。
**迷ったらとりあえず `git status`** を打つ癖をつけましょう。

過去のセーブポイント（コミット）の一覧も見てみます。

```bash
git log --oneline
```

> 💡 `--oneline` は **one line（1行）** の意味で、1つのコミットを「短いコミットID + メッセージ」の1行で表示します。
> 付けずに `git log` だけだと、作成者や日時も含めて1件が何行にもなります。
>
> ```
> 3f2a9c1 Initial commit
> ```
>
> テンプレートから作ったリポジトリは、元のリポジトリの履歴を引き継がず、この「Initial commit」1件から始まります。コミット ID（先頭の英数字）は人によって違います。
>
> ログが長くて画面が止まったように見えたら **`q` キーで閉じられます**（上下の移動は矢印キー）。


## 2. 練習用のブランチを作る

`main` を直接いじらず、練習用ブランチで作業します。

```bash
git switch -c practice/自分の名前
# 例: git switch -c practice/taro
```

> 💡 ブランチ名に自分の名前を入れておくと、チームで同じリポジトリを使うときに誰のブランチか分かります。名前は半角英数字にしておくと扱いやすいです。

> 💡 `-c` は **create（作成）** の略で、「新しいブランチを作って、そこに移動する」という意味です。
>
> | コマンド | 動き |
> | --- | --- |
> | `git switch -c 新しいブランチ名` | まだないブランチを**作って**移動する |
> | `git switch 既存のブランチ名` | すでにあるブランチに移動するだけ |
>
> 古い記事でよく見る `git checkout -b ブランチ名` も同じ動きです。

今どのブランチにいるか確認します（`*` が付いているのが現在地）。

```bash
git branch
```


## 3. ファイルを作って変更を確認する

練習用のファイルを作ります。

```bash
echo "Gitの練習です" > docs/handson/practice.txt
git status
```

`Untracked files:` に `practice.txt` が赤字で出ます。
「Git がまだ管理していない新しいファイルがあるよ」という意味です。


## 4. add して commit する（セーブ）

```bash
git add docs/handson/practice.txt
git status
```

緑字になれば「次のコミットに含める」準備完了です。

```bash
git commit -m "練習用ファイルを追加"
git log --oneline
```

一番上に今のコミットが追加されていれば成功です。



## 5. ファイルを修正して差分を見る

```bash
echo "2行目を追加しました" >> docs/handson/practice.txt
git diff
```

`+` で始まる行が追加された部分です。確認できたらまたセーブします。

```bash
git add docs/handson/practice.txt
git commit -m "練習用ファイルに2行目を追加"
```

> 💡 変更したファイルをまとめて add したいときは `git add .`（カレントフォルダ以下すべて）。
> ただし意図しないファイルまで入りやすいので、慣れるまでは `git status` で確認してから使いましょう。


## 6. GitHub に送る（push）

初回だけ、GitHub 側にもブランチを作る指定（`-u origin ブランチ名`）が必要です。

```bash
git push -u origin practice/自分の名前
```

2回目以降は `git push` だけで OK です。
Codespaces では GitHub の認証が済んでいるので、パスワード入力は不要です。

GitHub で自分のリポジトリのページを開くと、ブランチが増えているのが確認できます。


## 7. プルリクエスト（PR）を作る（任意）

GitHub 上で「このブランチの変更を main に取り込んでください」と依頼する仕組みです。

1. GitHub で自分のリポジトリのページを開く
2. 表示される「Compare & pull request」ボタンを押す
   - ボタンが出ていなければ、「Pull requests」タブ →「New pull request」で、compare に練習用ブランチを選ぶ
3. 取り込み先（base）が自分のリポジトリの `main` になっていることを確かめる
4. タイトルと説明を書いて「Create pull request」

練習なので、作った PR はマージせずに「Close pull request」で閉じて構いません。


## 8. 片付け

`main` に戻り、練習用ブランチを削除します。

```bash
git switch main
git branch -D practice/自分の名前                  # 手元のブランチを削除
git push origin --delete practice/自分の名前       # GitHub 上のブランチを削除
```

`main` に戻ると `docs/handson/practice.txt` は消えています（練習用ブランチにしか存在しないため）。


## 困ったとき

| 状況 | コマンド |
| --- | --- |
| 今どうなっているか分からない | `git status` |
| 編集したファイルを最後のコミットの状態に戻したい | `git restore ファイル名` |
| add を取り消したい | `git restore --staged ファイル名` |
| 直前のコミットメッセージを直したい（push 前のみ） | `git commit --amend -m "新しいメッセージ"` |
| GitHub の最新を取り込みたい | `git pull` |

> ⚠️ `git reset --hard` や `git push --force` は変更が消えたり他人の作業を上書きしたりする危険なコマンドです。意味が分かるまでは使わないでください。


## よく使うコマンドまとめ

```bash
git status                     # 状態確認
git switch -c ブランチ名        # ブランチを作って移動
git switch ブランチ名           # ブランチを移動
git add ファイル名              # コミットするファイルを選ぶ
git commit -m "メッセージ"      # セーブ
git log --oneline              # 履歴を見る
git diff                       # 変更内容を見る
git push                       # GitHub に送る
git pull                       # GitHub から取り込む
```
