- master
本番環境の内容。リリースしているコードが入っている。
masterブランチに直でコミットすることは無い(masterのエラー修正とかもPRの形が基本)。
maaterをいじってしまうとデプロイに影響してしまう。

- developブランチを使う。

`git push リモート名 ブランチ名`はあまりしない方が良い

- upstreamブランチを設定しておけば良い。
→リモートブランチをsuggestしてくれる。

To push the current branch and set the remote as upstream, use
    git push --set-upstream origin feature
 
```terminal
(feature)$ git push
fatal: The current branch feature has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin feature

(feature)$ git push --set-upstream origin feature
Branch 'feature' set up to track remote branch 'feature' from 'origin'.
Everything up-to-date
(feature)$ git push
Everything up-to-date
(feature)$ git pull
Already up to date.
```

- Your branch is behind 'origin/develop' by 9 commits, and can be fast-forwarded.
→ローカルのdevelopブランチは、`origin/develop`から9コミット古い状態にあることがわかる。

- `git log`を実行
→ローカルのdevelopブランチの最新コミットが、forkしてcloneした時のものであることが分かる。

```
(feature)$ git checkout develop
Switched to branch 'develop'
Your branch is behind 'origin/develop' by 9 commits, and can be fast-forwarded.
  (use "git pull" to update your local branch)
(develop)$ git branch
* develop
  feature
  master
(develop)$ git log
commit 5a7a21046e96d6aae4efec6295d09fbb6724361b (HEAD -> develop, master)
Merge: 5a16939 c12f1f6
Author: YUJI ITO <yuji91.1227@gmail.com>
Date:   Mon Mar 16 19:05:27 2020 +0900

    Merge pull request #2 from runteq/dependabot/bundler/loofah-2.3.1
    
    Bump loofah from 2.3.0 to 2.3.1

commit c12f1f6fe3c4d27da8305c311e0f902ddd44d844
Merge: f2cbae0 5a16939
Author: YUJI ITO <yuji91.1227@gmail.com>
Date:   Mon Mar 16 19:05:14 2020 +0900

    Merge branch 'develop' into dependabot/bundler/loofah-2.3.1
```

`git pull`した後の、`git log`を見ると、`commit beeedff3dc3ad00c35e9af9300e413831749a929
(HEAD -> develop, origin/develop, origin/HEAD)`で、1回のgit pullにより、リモート上の9コミット分がmergeされたことが分かる。

```
(develop)$ git status
On branch develop
Your branch is behind 'origin/develop' by 9 commits, and can be fast-forwarded.
  (use "git pull" to update your local branch)
nothing to commit, working tree clean

(develop)$ git pull
Updating 5a7a210..beeedff
Fast-forward
 .rspec                      |  2 ++
 Gemfile                     |  2 ++
 Gemfile.lock                | 25 ++++++++++++++++++++++
 spec/factories/tasks.rb     |  8 +++++++
 spec/models/task_spec.rb    | 34 ++++++++++++++++++++++++++++++
 spec/rails_helper.rb        | 67 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 spec/spec_helper.rb         | 96 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 spec/support/factory_bot.rb |  0
 8 files changed, 234 insertions(+)
 create mode 100644 .rspec
 create mode 100644 spec/factories/tasks.rb
 create mode 100644 spec/models/task_spec.rb
 create mode 100644 spec/rails_helper.rb
 create mode 100644 spec/spec_helper.rb
 create mode 100644 spec/support/factory_bot.rb
(develop)$ git log
commit beeedff3dc3ad00c35e9af9300e413831749a929 (HEAD -> develop, origin/develop, origin/HEAD)
Merge: b858da3 055e1c7
Author: ryota1116 <ryota1116funa@gmail.com>
Date:   Mon May 4 14:25:46 2020 +0900

    Merge pull request #3 from ryota1116/feature
    
    [Fix]Taskモデルのバリデーションチェックのテストを修正
```

### upstreamブランチを設定しておくと便利
- `git push origin ...`のoroginを指定しない。
- `git push origin 100`と`git push origin 101`を間違える可能性も。
- remoteのブランチに影響することはしないこと。suggestに従う。


### commitの単位を考える。
- レビュー前
ザックリ出すのもあり
設定ファイルでコミット、実装でコミット、テストでコミット等、レビュアーが見やすいようにコミットする。

- レビュー後の指摘事項に対するコミット
指摘事項の種類でにコミット(とメッセージ)を分ける(テスト、FactoryBotで分けたり)

