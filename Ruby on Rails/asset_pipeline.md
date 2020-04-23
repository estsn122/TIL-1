# アセットパイプラインとは
- ディレクトリやファイルを作業しやすいように分けて作成したassets内のファイル(css,j,imageなど)を、一つにまとめる(効率的)ことでwebページに表示できるようにする機能。
→この連結・圧縮された一つのファイルが、erbテンプレートからHTML化されたファイルと紐付いてブラウザ画面に表示される。



## ブラウザに読み込む

```erb
(app/views/layouts/admin.html.erb)
<!DOCTYPE html>
<html lang="en">

  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta http-equiv="x-ua-compatible" content="ie=edge">
    <title>AdminLTE 3 | Starter</title>
    <!-- Google Font: Source Sans Pro -->
    <link href="https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,400i,700" rel="stylesheet">
    <!-- 読み込むマニフェストファイルを指定 -->
    <%= stylesheet_link_tag    'admin', media: 'all' %>
    <%= javascript_include_tag 'admin' %>
  </head>

  <body class="hold-transition sidebar-mini">
    <!-- yield部分に各コントローラーで表示するテンプレートが表示される。-->
    <%= yield %>
  </body>

</html>
```

```scss
(app/assets/stylesheets/admin.scss)
// ここでcssファイルを結合する
@import 'admin-lte/plugins/fontawesome-free/css/all.min.css';
@import 'admin-lte/dist/css/adminlte.min.css';
```

## 一つのファイルに結合
- マニフェストファイルにファイルをどのように連結するか(どんなファイルを連結するか)記述する。

- マニフェストファイルが/node_modulesディレクトリを読み込めるように、探索パスを設定。

```ruby
(config/initializers/assets.rb)
Rails.application.config.assets.paths << Rails.root.join('node_modules')
```

- 探索パスの確認
```
[2] pry(main)> puts Rails.application.config.assets.paths
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/app/assets/config
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/app/assets/images
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/app/assets/javascripts
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/app/assets/stylesheets
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/vendor/bundle/ruby/2.6.0/gems/i18n-js-3.6.0/app/assets/javascripts
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/vendor/bundle/ruby/2.6.0/gems/jquery-rails-4.3.5/vendor/assets/javascripts
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/vendor/bundle/ruby/2.6.0/gems/actioncable-5.2.3/lib/assets/compiled
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/vendor/bundle/ruby/2.6.0/gems/activestorage-5.2.3/app/assets/javascripts
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/vendor/bundle/ruby/2.6.0/gems/actionview-5.2.3/lib/assets/compiled
/Users/funesakisuke/workspace/runteq/168_ryota1116_runteq_learning_basic/node_modules
...
```

- マニフェストファイルは探索パスをもとに、結合するファイルを指定できる
→node_modules/*が探索パスに設定された。

```js
(app/assets/javascripts/application.js)
//= require 'admin-lte/dist/js/adminlte.min.js'
```
→`/node_modules/admin-lte/dist/js/adminlte.min.js`をマニフェストファイルに結合。


## プリコンパイル
```ruby
(config/initializers/assets.rb)
# プリコンパイルしたいファイルを指定
Rails.application.config.assets.precompile += %w( admin.css )
Rails.application.config.assets.precompile += %w( admin.js )
```
