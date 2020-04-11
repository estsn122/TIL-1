- ## コレクションをeachで繰り返して部分テンプレートをレンダーすることは、パフォーマンス上も見た目としても、良くない。
#### 1. 一覧画面を表示させる(BAD例)
```
(app/views/boards/show.html.erb)
1  <%= @comments.each do |comment| %>
2     <%= render 'comments/comment', { comment: comment } %>
3  <% end %>
```
2行目{ comment: comment }の、一つ目のcommentが呼び出し先で使える変数。

#### 2. このように書き換え可能。
```
(app/views/boards/show.html.erb)
<%= render @comments %>
```

```
(app/views/comments/_comment.html.erb)
<p><%= comment.body %></p>
```

##### この書き方を使える条件
- 呼び出し先の部分テンプレートが、commentsフォルダ内の、_comment.html.erbであること。
- その呼び出し先で使える変数はcomment

- ## N+1問題
#### N+1問題とは、大量のSQLが実行されて動作が重くなるという問題。
→「全てのレコードを取得する1回+N回SQLが実行される」ために、N+1と呼ばれる。

##### ex.)ツイートを一覧表示させたい
①tweetをallで取得(１回)
②その各ツイートをしたユーザー名も表示させたい
→Usersテーブルから取得する
→ツイートの数の分だけusersテーブルから名前を取得するSQLを実行してしまう(ツイート N回分)
```
Started GET "/boards" for ::1 at 2020-03-20 16:52:47 +0900
Processing by BoardsController#index as HTML
  Rendering boards/index.html.erb within layouts/application
  Board Load (1.2ms)  SELECT "boards".* FROM "boards"
  ↳ app/views/boards/index.html.erb:17
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  ↳ app/views/boards/index.html.erb:34
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  ↳ app/views/boards/index.html.erb:34
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 3], ["LIMIT", 1]]
  ↳ app/views/boards/index.html.erb:34
  ・
  ・
  ・
```
##### 解決策
・includesメソッドで関連するモデルのデータを先に取得しておく。
→eachメソッドで繰り返し表示させた場合でも、毎回SQLを発行せずに済む。
```
@boards = Board.all.includes(:user)
```
```
Started GET "/boards" for ::1 at 2020-03-20 17:19:28 +0900
Processing by BoardsController#index as HTML
  Rendering boards/index.html.erb within layouts/application
  Board Load (0.3ms)  SELECT "boards".* FROM "boards"
  ↳ app/views/boards/index.html.erb:17
  User Load (0.4ms)  SELECT "users".* FROM "users" WHERE "users"."id" IN (?, ?, ?, ?, ?, ?, ?, ?, ?, ?)  [["id", 1], ["id", 2], ["id", 3], ["id", 4], ["id", 5], ["id", 6], ["id", 7], ["id", 8], ["id", 9], ["id", 10]]
  ↳ app/views/boards/index.html.erb:17
  Rendered boards/index.html.erb within layouts/application (51.0ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 10], ["LIMIT", 1]] （※ログイン中のユーザー）
  ↳ app/views/layouts/application.html.erb:13
  Rendered shared/_header.html.erb (2.5ms)
  Rendered shared/_flash_message.html.erb (0.4ms)
  Rendered shared/_footer.html.erb (0.4ms)
Completed 200 OK in 106ms (Views: 102.6ms | ActiveRecord: 0.9ms)
```
##### bulletジェムで、N+1問題を検出してくれる(発生時にブラウザに警告を表示)
