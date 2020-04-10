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
