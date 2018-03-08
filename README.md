# Gitメモ
自分用のメモ。随時更新

## 用語
```
ワークツリー：実際に作業をしているディレクトリのこと
インデックス（ステージ領域）：リポジトリにコミットする準備をするための場所のこと
```

## git init
```
■ローカルリポジトリ
git init
■共有リポジトリ
git --bare init --shared
```

## バージョン確認

```bash
git --version
```

## git config

確認

```bash
git config --list
git config キー名
```

登録・更新

```bash
git config キー名 設定値 # ローカルリポジトリに設定
git config --global キー名 設定値 # すべてのローカルリポジトリに設定
```

削除

```bash
git config --unset キー名
```

## git add

### deletedをaddする

```
git add -u
```

### ファイル名を大文字⇔小文字で変更したらgitに反映されない

```
git mv(リネーム)を２回する

git mv kaisetsu.php _Kaisetsu.php
git mv _Kaisetsu.php Kaisetsu.php

http://ratememo.blog17.fc2.com/blog-entry-989.html
```

### .gitignoreを変更したのに反映されない場合の対処法

```
既にコミットしたファイルを管理対象外にしたいとき
.gitignoreに追加するが管理対象外にならない。

git rm --cachedを使って，インデックスからのみ削除する．
$ git rm -r --cached data/kaisai.csv data/kaisai_bk.csv
```

[参考]
[.gitignoreを変更したのに反映されない場合の対処法](http://alice345.hatenablog.com/entry/2013/09/07/001519)


### git add -p

変更箇所を部分的にadd(ステージング)したり、しなかったりする。

```
変更のひとかたまり(ハンク)を表示し、そのハンクに対しての指示を待ち受けます。サンプルが小さいのですべて表示されますが、ハンクの間が７行以上空くと別のハンクとして扱われます。

y - このハンクをステージングする
n - スキップする
q - 終了する
a - 以降のハンクをすべてステージングする
d - 以降のハンクをすべてスキップする
g - 指定したハンクへ移動
/ - 正規表現によるハンクの検索
j - 未確定な前のハンクへ移動する
J - 前のハンクへ移動する
k - 未確定な次のハンクへ移動する
K - ハンクへ移動する
s - ハンクを分割する
e - 手動で現在のハンクを修正する
? - ヘルプを表示する
```



## git commit
```
■commitメッセージを修正
git commit --amend
```

## git checkout

```
# ローカルの変更を元に戻す
# まだaddすらしていない状態だけど、行った変更を取り消したい。
git checkout ファイル名
git checkout .

# 特定のファイルを、あるコミットの時の状態に戻す
git checkout コミット番号 ファイル名

```
http://keyamb.hatenablog.com/entry/2013/02/08/105556

## git reset
### インデックスへの登録を取りやめたい（git addの反対）
```
git reset <paths>
```

### 最後のコミットの状態に戻したい
ファイルをインデックスから削除し、かつ作業内容も消してしまいたい場合に使用します
このコマンドで失われた内容はほどんとの場合元に戻すことが不可能になる

```
git reset --hard
```

### commit後、直前のコミットを取り消したい
一度はコミットしたものの、バグやtypoを発見しコミットをしなおしたい時
作業内容とインデックスはそのままに直前のコミットだけを削除する

```
git reset --soft HEAD~
```


※`git reset`はHEADの位置を指定したコミットに変更するコマンドです。
(`HEAD~`と指定した場合、HEADを指すコミットが直前コミットに変更される）
`--soft`オプションはHEADの位置のみを変更し、インデックスや作業ディレクトリには影響を与えません。
`--hard`オプションはHEADの位置に加えて、インデックスや作業ディレクトリの状態も元に戻します。


### commit後、直前のコミットを削除したい
```
git reset --hard HEAD~
```

### とにかく困った.とにかくリモートリポジトリの状態に戻したい
```
git reset --hard origin/master
```

### 削除したコミットを取り戻したい
```
git reflogでコミットIDが残ってるか確認

git reset コマンドでHEADの位置を変更します。
git reset --hard <コミットID>
```

### mergeを取り消し

マージ作業に行き詰まり、全ての処置を捨て去る場合には、 いつでもマージ前の状態に戻ることができます。

```
git reset --hard HEAD
```

既に削除したいマージ結果をコミット済みの場合には、次のようにします。

```
git reset --hard ORIG_HEAD
```

### １つ前のコミットで動作を確認したい(※)

```
git reset --hard HEAD~
```

--hardなので、「インデックス・ワーキングツリー」も1つ前のコミットの状態に戻る。

### 過去のコミットで動作を確認したい(※)

```
git reset --hard 戻したいコミットのハッシュ値
```

(※)これをやる前に、pushして置くこと。
`reset --hard`は基本的にすべて消えるので慎重に。
一応復活させる術はなくはない。
remoteに最新の状態を記録しておけば、ローカルは好きな所に移動しても安心。
動作確認後、最新の状態に戻るには、また、

```
git reset --hard ORIG_HEAD
```

をすれば良い。

`git reset --hard ORIG_HEAD`：ORIG_HEADは直前のHEADを表す。resetをなかったことにする。
`git reset`は未来の状態にも行ける。
`git reset --hard 最新のコミットのハッシュ値`でも良い。

### git commit --amendをキャンセルする
amendによるものはいったんcommitして、次に

```
$ git reset --soft HEAD@{1}
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

ORIG_HEAD:git mergeやgit resetなどの操作を行うと上述のHEADが移動してしまうが、
ORIG＿HEADを使うことで移動前のHEADを指定できる
```

## git config

```
# リモートリポジトリのURLを変更
origin のURLを変更
git remote set-url origin git@bitbucket.org:dondoko_susumu/hogehoge.git

# alias
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

## git log ワンライナーで指定期間のコミット回数を取得

```
git log --since=2016-06-01 --until=2016-12-31 --oneline --author='Ken Kubota' --no-merges | wc -l
```

## git diff

```
# ワークツリーとステージ領域の差分
git diff <file>

# ワークツリーと最新コミットの差分(HEAD:作業しているブランチの最新のコミットを参照しているポインタ)
git diff HEAD <file>

# ステージング（インデックス）とコミットのdiff
git diff --cached

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
### どのブランチがマージされているのかを知りたい

リモートのmasterブランチにマージされているリモートブランチの一覧を表示できます。

```
git branch -r --merged origin/master
```

リモートのmasterブランチにまだマージされてないリモートブランチの一覧を表示できます。

```
git branch -r --no-merged origin/master
```

### ブランチ名の変更

```
git branch -m <oldbranch> <newbranch>
```

### Gitでブランチを作るのを忘れてmasterにコミットしてしまったときの対処法

```
git checkout -b foo
git checkout master
git reset origin/master --hard
```

cherry-pickでの対処法  
[Gitでブランチを作るのを忘れてmasterにコミットしてしまったときの対処法](http://qiita.com/atskimura/items/a90dfa8bfc72e3657ef9)

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

### マージ時のコンフリクトで片側の変更だけ適用する方法
`checkout --ours` と `checkout --theirs` を使い分ければ良い。

```
# ２つのブランチ間でコンフリクトしているファイル fileA.txt と fileB.txt があるとする

# fileA.txt を現在チェックアウトしているブランチ側の対応に合わせる場合
$ git checkout --ours fileA.txt
$ git add fileA.txt    # add を忘れずに

# fileB.txt をマージさせたブランチ側に合わせる場合
$ git checkout --theirs fileB.txt
$ git add fileB.txt

$ git commit
```

[[git]マージ時のコンフリクトで片側の変更だけ適用する方法](http://qiita.com/nantekkotai/items/2ed17c3d774211d234a4)

## git rebase

リベースは一連のコミットを再配置する処理です。処理の結果はマージと似ていますが、処理内容は異なります。
他のメンバーがプッシュしたコミットを取り込んだ後、自分のコミットを先頭に再配置するために使います。
-iオプションを使うことでローカルコミット履歴を理解しやすいように整理することも可能


### トピックブランチでの作業中にmasterブランチで起こった変更を取り込みたい

自分のブランチできりのいいところで一旦コミット。そこから以下を実行。

```
git checkout master
git pull origin master
git checkout [your_working_branch]
git rebase master
```

Web+DB Press Vol.90 P.27
masterブランチをrebaseで取り込んだ後、マスターにマージ

```
git checkout topic
git rebase master //topicよりが進んでしまったmasterの先頭にtopicを再配置
git checkout master
git merge master //再配置したtopicブランチをマージ
```


※ やってはいけないリベース
「すでにプッシュされたコミットのリベース」です。リベースすると別コミットになるため問題が起きる

### マージとリベースの使い分け

■topicブランチでの作業が完了してmasterブランチに統合したい

```
-> merge
topicをmasterにmergeすることで、ブランチでの作業履歴を残した方が良い  
```

■topicブランチでの作業中にmasterブランチで起こった変更を取り込みたい

```
-> rebase
ここでmeegeを行うと「取り込んだだけ」の変更履歴が残ってしまい、不必要に履歴を複雑にしてしまうためです。
```

■作業中のブランチに対して他のチームメンバーによる変更があり、それをリモートリポジトリから取り込みたい

```
-> rebase
単純にpullしてしまうとそのブランチでの直接の作業とは言えない不必要なマージコミットが発生してしまう。
git pull --rebaseを使う。
```

Web+DB Press vol.90 P.29


## git rebase -i
### 過去の任意のコミットを削除したい
```
(HEAD~2/最新コミットより２世代前より後のコミットを変更する)
git rebase -i HEAD~2
(対象となるコミットの行そのものを削除または、コメントアウトし、保存・終了)
```

### 過去のコミットの内容を変更したい

```
git rebase -i HEAD~2
```

変更したいコミットのpickをeditに変更して保存・終了します
editはコミットを変更するためのコマンドで、リベースが一旦止まりコミットに変更を加えることができます。
（つまり、editのコミット直後の状態になる）

ファイル名を変更したり、新たにファイルを追加したり、内容修正したりできます。

```
git mv a.txt 3-1.txt
```

内容変更した後はコミットメッセージも変更します。

```
git commit --amend -m '3-1.txtを追加'
```

リベースを終了するため↓を実行

```
git rebase --continue
```

複数のコミットをeditで変更する場合
コミット内容を変更 → `git commit --amend` → `git rebase --continue`
を繰り返すことになります

[過去のコミットのコメントだけを修正したい](http://www.backlog.jp/git-guide/reference/log.html#sec4)

### 複数のコミットをまとめたい
```
git rebase -i HEAD~2
pick -> squashにして保存
squashが指示されたコミットは１つ前のコミットとまとめられます。
保存すると再度エディタが立ち上がり、まとめられて１つになるコミットのコメントを書くことができます。
```

### コミットの順序を入れ替える
```
これまでのコミットを確認
git log -n 4 --oneline --reverse
git rebase -i HEAD~4
pickの行を入れ替えて保存
```
[gitでコミットの順序を入れ替える](http://labs.timedia.co.jp/2010/11/git-reorder-commits.html)

## git pull
### git pullで、ローカルを強制上書きする方法

```
// 1) リモートの最新を取ってきておいて・・
git fetch origin master

// 2) ローカルのmasterを、リモート追跡のmasterに強制的に合わせる！
git reset --hard origin/master
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

## git revert
### push後、安全にコミットを取り消したい
```
プッシュ後にコミットを取り消す方法は
git push -f
git push --force-with-lease
などがありますが、どちらにも言えるのは「コミットを削除した」というコミット履歴が残らない点です。
共用ブランチにこの操作を行った場合は、そのことをチームメンバーに伝える必要があるし、
コミットの削除をチームメンバーのローカルブランチにも反映させるためには、強制的なgit pull を全員に
行ってもらう必要があります。これはチームメンバーがローカルブランチで行っているコミットを誤って
削除してしまうなど、たいへん危険な操作です。

安全にコミットを取り消したい場合は、  
git revert
を使用するとよいでしょう。コミットを削除するのではなく、コミットを打ち消す新たなコミットを作ります。
HEAD~3で３つ前のコミットを指定したり、git log で調べたコミットIDを指定するとよいでしょう。

例）2つ前のコミットを打ち消す
git revert HEAD~2

指定したコミットを打ち消すコミットが作られる
エディタが起動しコメントを保存→打ち消しコミットが作られる。
あとは通常通り、git pushする。

```

## git push
### ローカルブランチをリモートに反映する

```
git push origin hoge

省略しない書き方
git push origin hoge:hoge

ローカルのhogeブランチをリモートのhogeブランチにpush！です。
git push {ローカルのhoge}:{リモートのhoge}
```
[今さら聞けないgit pushコマンド](http://shoma2da.hatenablog.com/entry/2014/03/08/234523)


### push済みコミットの変更を反映したい  
```
git push --force-with-lease
```


リモートリポジトリにプッシュ済みのコミットを変更・削除した場合
ほかのユーザが何か新たなコミットをプッシュしてない場合のみ※、リモートブランチを上書きしてくれる

* ほかのユーザがコミットをプッシュしていた場合は、プッシュはリジェクトされる
git push -f の場合はこのチェックは行われないため、ほこのユーザーのコミットを削除してしまいます。

* 複数のユーザが使用している共有ブランチでの過去のコミットの変更・削除は多くの場合問題を引き起こします。
自身しか参照していないブランチでのみ行うようにするのがよいでしょう。


### 不要になったリモートブランチを削除する

まずはローカルブランチを削除

```
$ git branch -d hoge
Deleted branch hoge (was 57c1b0b).
```

リモートブランチも削除

```
$ git push origin :hoge
To https://xxx.git
 - [deleted]         hoge
```
[Git で不要になったリモートブランチを削除する](http://blog.koogawa.com/entry/2014/03/08/121751)
[今さら聞けないgit pushコマンド](http://shoma2da.hatenablog.com/entry/2014/03/08/234523)


### tracking branch（追跡ブランチ）の削除

`git push ORIGIN-NAME :BRANCH-NAME`でリモートは消せるけど、どうやらローカルにあるtrackingは残ってるようだ。これ消したい。

`git branch -r -d ORIGIN-NAME/BRANCH-NAME`

```
git branch -a
* develop
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/develop
  remotes/origin/feature/hoge

git branch -r -d origin/feature/hoge
Deleted remote branch origin/feature/hoge (was 691026f).
```

### git fetch --prune
リモートで消されたブランチが手元で残ってしまう件を解消する
`git fetch`もしくは`git pull`に`--prune`オプションをつける
`--prune`オプションをつけると、fetchやpullする際に自動的にリモートで削除されているリモートブランチを削除してくれる。

```
$ git fetch --prune
 x [deleted]         (none)     -> origin/hoge
```

または、git remote pruneを実行する

```
$ git remote prune origin
 x [deleted]         (none)     -> origin/hoge
```

[リモートで消されたブランチが手元で残ってしまう件を解消する](http://qiita.com/yuichielectric/items/84cd61915a1236f19221)


## git filter-branch --commit-filter
### 名前、メールアドレスを変更したい
`git filter-branch --commit-filter`は、すべてのコミットに対して何らかの処理を行いたい場合に使用します。

```
例）環境変数$GIT_AUTHOR_EMAILが特定のメールアドレスに該当するコミットのみ
　「名前」「メールアドレス」に関する変数を変更するコマンドです。

git filter-branch -f --commit-filter '
if [ "$GIT_AUTHOR_EMAIL" = "old@example.com" ]; then
  GIT_AUTHOR_NAME="新しいAuthor名"
  GIT_AUTHOR_EMAIL="new@example.com"
  GIT_COMMITER_NAME="新しいCommiter名"
  GIT_COMMITER_EMAIL="new@example.com"
fi
git commit-tree "$@"
' HEAD
```

* なお、`filter-branch`を使用する場合は、テスト用ブランチで順分確認して使うこと
* また、`--all`オプションを付けるとすべてのブランチに対して`filter-branch`が実行されます。
* `filter-branch`は歴史を書き換えるコマンドです。また、コミットIDも変わってしまいます。
　リモートブランチにすでにプッシュ済である場合は`git push --force-with-lease`などで
　強制的にプッシュしない限り反映されませんが、共用ブランチで行うのはやめたおいたほうが無難です。


## git filter-branch --tree-filter
### パスワードが含まれるファイル(password.txt)をコミットしてしまった
```
歴史から完全にpassword.txtを削除するには
git filter-branch --tree-filterを使用します。
すべてのツリーオブジェクトに対して処理行うことができます。

例)すべてのツリーに対してrm -f password.txtを実行する

git filter-branch --tree-filter 'rm -f password.txt' HEAD

git log --oneline --name-only (ファイル名の確認)

HEADの部分はブランチ名を指定することができます。
(HEADはカレントブランチへの参照となっているため、ブランチの代わりに指定できます)

```

## git cherry-pick
### コミットするブランチを間違えた
指定したコミットの変更内容をカレントブランチに取り込むコマンドです。
本来コミットしたかったブランチで次のコマンドを実行することで、別ブランチのコミット内容を取り込みましょう。

```
git logなどで取り込みたいコミットのコミットIDを特定したあと

git cherry-pick <コミットID>
```


これで、カレントブランチに指定したコミットが取り込まれます。
そのあと、間違えてコミットしていたブランチから、該当するコミットを削除しましょう。

## git 本家リポジトリをupstreamから最新ソースにする

#### 1. 本家リポジトリをupstreamとしてremote add
```
$ git remote add upstream git://github.com/github-book/first-pr

$ git remote -v
origin	https://github.com/dondoko-susumu/first-pr.git (fetch)
origin	https://github.com/dondoko-susumu/first-pr.git (push)
upstream	git://github.com/github-book/first-pr (fetch)
upstream	git://github.com/github-book/first-pr (push)
```

#### 2. fetch upstream

```
$ git fetch upstream
remote: Counting objects: 20, done.
remote: Compressing objects: 100% (16/16), done.
remote: Total 20 (delta 10), reused 12 (delta 4)
Unpacking objects: 100% (20/20), done.
From git://github.com/github-book/first-pr
 * [new branch]      gh-pages   -> upstream/gh-pages
```

#### 3. merge先のbranchへ移動

```
$ git branch gh-pages
```

#### 4. merge

```
$ git merge upstream/gh-pages
Updating 02e9685..8c87bff
Fast-forward
 index.html | 4 +++-
 1 file changed, 3 insertions(+), 1 deletion(-)
```

## No newline at end of file

`git diff`すると、ファイルの最終行で同じ内容が削除され追加された事になるような±と`No newline at end of file`というメッセージが出る事がある。Vimは最終行に改行を付けるエディタなので、最終行に改行がないファイルをVimで編集すると、これが出ます。
ファイル末尾に改行を入れるのがPOSIXの流儀と書いてるとこもあったので、Vimに限らずかもしれない。


#### 対応
vim でファイルを開き、コマンドモードで下記を実行して保存します。

```
:set binary noeol
```
[git :: 「No newline at end of fileの対処](http://tm.root-n.com/unix:command:git:operation:no_newline_at_end_of_file)

## リモートブランチをローカルにチェックアウトする

```
$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/hoge
  remotes/origin/master
```

hogeブランチがローカルにないのでチェックアウトしたい場合。

```
$ git checkout -b hoge origin/hoge
```

remotes/ は不要。-b オプションを付けると自動的にそのブランチに切り替わる。

## git stash

```
退避
$ git stash <save>
```

```
ストックの確認
$ git stash list
 stash@{0}: WIP on sub: a0d2f1b add fourth line
 stash@{1}: WIP on sub: 1a61919 add second line

<stash名>: WIP on <stashを行ったブランチ名>: <ハッシュ> <コミットコメント>
```

```
ストックから復活
$ git stash apply stash@{0}
```

```
ストックの復活（勝手にマージする）
$ git stash pop stash@{0}
```

```
ストックの削除
$ git stash drop <消したいstash名>
```

[変更を一時的に退避！キメろgit stash](http://qiita.com/fukajun/items/41288806e4733cb9c342)

#### gitで未追跡（untracked)なファイルもまとめて退避(stash)する

```
uオプション
$ git stash -u
```

[gitで未追跡（untracked)なファイルもまとめて退避(stash)する](http://qiita.com/muran001/items/f5746c518bf663f86a79)

## オプション

|ショートオプション | ロングオプション | 説明 |
|--------|--------|--------|
| –-     |        |オプションとファイルを明示的に切り分けます。例えば「-n」という名前のファイルをステージする場合に利用します。|

## bitbucketからGithubに移行
GitHubにはインポート機能があるのでBitbucketのURLをいれるだけで簡単に移行できる。

[https://import.github.com/](https://import.github.com/)

URLは`https://bitbucket.org/dondoko_susumu/hoge`といったBitbucketのプロジェクトページのURLをいれる。

[BitbucketからGitHubにリポジトリを自動インポートで移行する。](http://final.hateblo.jp/entry/2016/01/16/233135)

