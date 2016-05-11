# git チートシート

## 用語
```
ワークツリー：
インデックス（ステージ領域）：
```

## git init
```
■ローカルリポジトリ
git init
■共有リポジトリ
git --bare init --shared
```

## git add
```
■deletedをaddする
git add -u

■ファイル名を大文字⇔小文字で変更したらgitに反映されない
対応方法
git mv(リネーム)を２回する

git mv kaisetsu.php _Kaisetsu.php
git mv _Kaisetsu.php Kaisetsu.php

http://ratememo.blog17.fc2.com/blog-entry-989.html

■.gitignoreを変更したのに反映されない場合の対処法
[参考]
http://alice345.hatenablog.com/entry/2013/09/07/001519

既にコミットしたファイルを管理対象外にしたいとき
.gitignoreに追加するが管理対象外にならない。

git rm --cachedを使って，インデックスからのみ削除する．
$ git rm -r --cached data/kaisai.csv data/kaisai_bk.csv
```

## git commit
```
■commitメッセージを修正
git commit --amend
```

## git checkout
```
■ローカルの変更を元に戻すには
まだaddすらしていない状態だけど、行った変更を取り消したい。
git checkout ファイル名
git checkout .

[参考]
http://keyamb.hatenablog.com/entry/2013/02/08/105556
```

## git reset
```
■インデックスへの登録を取りやめたい（git addの反対）
git reset <paths>

■最後のコミットの状態に戻したい
ファイルをインデックスから削除し、かつ作業内容も消してしまいたい場合に使用します
このコマンドで失われた内容はほどんとの場合元に戻すことが不可能になる
git reset --hard

■commit後、直前のコミットを取り消したい
一度はコミットしたものの、バグやtypoを発見しコミットをしなおしたい時
作業内容とインデックスはそのままに直前のコミットだけを削除する
git reset --soft HEAD~

※git reset はHEADの位置を指定したコミットに変更するコマンドです。
(HEAD~と指定した場合、HEADを指すコミットが直前コミットに変更される）
--softオプションはHEADの位置のみを変更し、インデックスや作業ディレクトリには影響を与えません。
--hardオプションはHEADの位置に加えて、インデックスや作業ディレクトリの状態も元に戻します。

■commit後、直前のコミットを削除したい
git reset --hard HEAD~

■とにかく困った.とにかくリモートリポジトリの状態に戻したい
git reset --hard origin/master
```

## git remote
```
■リモートリポジトリの確認
$ git remote -v
origin  admin@ww1.boatrace-special.jp:/home/admin/git/2014TJC.git (fetch)
origin  admin@ww1.boatrace-special.jp:/home/admin/git/2014TJC.git (push)
```

## コミットの指定方法
```
HEAD：現在のブランチの最新コミット
~：他のコミット指定と組み合わせて、そのコミットの親を指定できる。数字を付けることで「何代前の親か」指定可能

HEAD~：HEADの親
HEAD~1：HEADの親(1世代前)
HEAD~2：HEADの親の親(2世代前)

ORIG_HEAD:git mergeやgit resetなどの操作を行うと上述のHEADが移動してしまうが、ORIG＿HEADを使うことで移動前のHEADを指定できる
```

## git config

## リモートリポジトリのURLを変更
origin のURLを変更
```
git remote set-url origin git@bitbucket.org:dondoko_susumu/hogehoge.git
```

## git alias
```
git config --global alias.co checkout
git config --global alias.st status
git config --global alias.br branch
git config --global alias.cm commit
git config -l
```

## git log

```
# 1行表示
git log --oneline

# 1行表示&ファイル名の表示
git log --oneline --name-only

# コミット間の差分を表示
git log -p

# コミット間の差分を表示<ファイル>
git log -p <ファイル名>

# どのファイルが何箇所変わったかというのを見ることができます。
git log --stat

# リネームしたファイルの履歴まで見る
git log --oneline --follow file3.txt

#特定のAuthorのコミットだけ表示する
git log --author <検索したい名前>

#コミット履歴を文字列で検索する
文字列が削除されたコミットでも追加されたコミットでもdiffの中に含まれれば表示されます
git log -S "if (a="
```

## git diff

```
# ワークツリーとステージ領域の差分
git diff <file>

# ワークツリーと最新コミットの差分(HEAD:作業しているブランチの最新のコミットを参照しているポインタ)
git diff HEAD <file>

# ステージング（インデックス）とコミットのdiff
git diff --chached

# コミット間のDiff
git diff <比較元コミット> <比較先コミット>

# コミット間のDiff(ファイル名だけ)
git diff <比較元コミット> <比較先コミット> --name-only

# diff ファイル名だけ表示
git diff  比較ブランチ名 --name-only

# Pull Requestで表示される差分(<比較先／比較元コミットの共通の祖先>　と　<比較先コミット>)
git diff <比較元コミット>...<比較先コミット>
```

## git reflog

```
# リポジトリで行われた作業ログ
GitのGC（Garbage Collection）が行われない限り、このログである程度歴史を行き来できる
git reflog
```

## git blame
```
# 行単位で編集者を表示
git blame <ファイル名>
```

## git bisect

```
不具合が混入したコミットを見つけ出す

git bisect start <不具合コミット> <正常なコミット>

例）
git bisect start HEAD xxxxx
HEADとxxxxxの真ん中にあるコミットyyyyyがgoodかbadか尋ねられる

git bisect bad
yyyyyとxxxxxの真ん中にあるコミットjjjjjがgoodかbadか尋ねられる

git bisect good
これで最初に不具合が混入したコミットを特定できる

git bisect reset
作業終了
```

## git branch

```
# ブランチの表示
git branch
```

## git merge

```
マージアリゴリズムはGitによって自動的に選択される

# 3方向マージ（3-way merge）
マージコミットができる

# 早送りマージ（fast-forward merge）
マージコミットを作らない。HEADまでのすべての履歴をマージ先のブランチが持っている場合に早送りマージになる

# ブランチをマージ
ブランチからマージしたことを明確に歴史を残すためにマージコミットを作成する
git merge --no-ff feature-A
```

## git rebase

```
リベースは一連のコミットを再配置する処理です。処理の結果はマージと似ていますが、処理内容は異なります。
他のメンバーがプッシュしたコミットを取り込んだ後、自分のコミットを先頭に再配置するために使います。
-iオプションを使うことでローカルコミット履歴を理解しやすいように整理することも可能

Web+DB Press Vol.90 P.27

# トピックブランチでの作業中にmasterブランチで起こった変更を取り込みたい
git checkout topic
git rebase master //topicよりが進んでしまったmasterの先頭にtopicを再配置
git checkout master
git merge master //再配置したtopicブランチをマージ

※やってはいけないリベース
「すでにプッシュされたコミットのリベース」です。リベースすると別コミットになるため問題が起きる

## マージとリベースの使い分け　Web+DB Press vol.90 P.29
「topicブランチでの作業が完了してmasterブランチに統合したい」
-> merge
topicをmasterにmergeすることで、ブランチでの作業履歴を残した方が良い

「topicブランチでの作業中にmasterブランチで起こった変更を取り込みたい」
-> rebase
ここでmeegeを行うと「取り込んだだけ」の変更履歴が残ってしまい、不必要に履歴を複雑にしてしまうためです。

「作業中のブランチに対して他のチームメンバーによる変更があり、それをリモートリポジトリから取り込みたい」
-> rebase
単純にpullしてしまうとそのブランチでの直接の作業とは言えない不必要なマージコミットが発生してしまう。
git pull --rebaseを使う。

```

## git pull
```
■pullした時に怒られた
error: Your local changes to 'admin/data/jyodir.sh' would be overwritten by merge.  Aborting.
Please, commit your changes or stash them before you can merge.

１つ前のコミットをpullしてなくて
１つ前のコミットで削除したjyodir.shのファイルが存在するので
マージしろと怒られている？
ローカルのjyodir.shを削除して１つ前の
```

## コンフリクト
```
# どのファイルがコンフリクトしているのかを調べる
git status

# コンフリクトを起こしたファイルに影響を与えたコミットを探す
git log --oneline --merge

# マージの中断
予期せずコンフリクトが発生した場合や、マージが間違っていると感じた場合は、git reset でマージを中断できる
git reset --hard HEAD

# マージの破棄
マージしてコミットした後に、その操作をなかったことにしたい場合は、ORIG_HEADを指定してgit resetコマンドを実行します。
git reset --hard ORIG_HEAD

# リベース時のコンフリクトの解消
git rebase topic (コンフリクト発生！)

git status

(コンフリクトのファイルを修正)
git add conflict_file.txt

git rebase --continue
(これによりリベースが再開されて、以降のコミットが再配置されます)

# conflictを無視してリベース
git rebase --skip

# rebase操作そのものが間違っていた場合,リベースを実行する前の状態に戻すことも可能
git rebase --abort


```
