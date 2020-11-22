# AdminLTEで管理画面を実装

## adminlteをyarnでインストール

```
$ yarn add admin-lte@^3.0                                                                                                                                                                      
yarn add v1.22.4
[1/4] 🔍  Resolving packages...
warning admin-lte > popper.js@1.16.1: You can find the new Popper v2 at @popperjs/core, this package is dedicated to the legacy v1
warning admin-lte > bootstrap-colorpicker > popper.js@1.16.1: You can find the new Popper v2 at @popperjs/core, this package is dedicated to the legacy v1
warning admin-lte > tempusdominus-bootstrap-4 > popper.js@1.16.1: You can find the new Popper v2 at @popperjs/core, this package is dedicated to the legacy v1
warning admin-lte > pdfmake > pdfkit > fontkit > babel-runtime > core-js@2.6.11: core-js@<3 is no longer maintained and not recommended for usage due to the number of issues. Please, upgrade your dependencies to the actual version of core-js@3.
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning "admin-lte > bootstrap-switch@3.3.4" has incorrect peer dependency "bootstrap@^3.1.1".
warning "admin-lte > tempusdominus-bootstrap-4@5.39.0" has unmet peer dependency "moment-timezone@^0.5.31".
warning "admin-lte > tempusdominus-bootstrap-4@5.39.0" has unmet peer dependency "tempusdominus-core@5.19.0".
[4/4] 🔨  Building fresh packages...
success Saved lockfile.
warning Your current version of Yarn is out of date. The latest version is "1.22.5", while you're on "1.22.4".
info To upgrade, run the following command:
$ brew upgrade yarn
success Saved 165 new dependencies.
info Direct dependencies
└─ admin-lte@3.0.5
info All dependencies
├─ @fortawesome/fontawesome-free@5.15.1
├─ @fullcalendar/bootstrap@4.4.2
├─ @fullcalendar/core@4.4.2
├─ @fullcalendar/daygrid@4.4.2
├─ @fullcalendar/interaction@4.4.2
├─ @fullcalendar/timegrid@4.4.2
├─ @lgaitan/pace-progress@1.0.7
├─ @sweetalert2/theme-bootstrap-4@3.2.0
├─ @ttskch/select2-bootstrap4-theme@1.3.4$ yarn add admin-lte@^3.0                                                                                                                                                                      【 admin_page 】
yarn add v1.22.4
[1/4] 🔍  Resolving packages...
warning admin-lte > popper.js@1.16.1: You can find the new Popper v2 at @popperjs/core, this package is dedicated to the legacy v1
warning admin-lte > bootstrap-colorpicker > popper.js@1.16.1: You can find the new Popper v2 at @popperjs/core, this package is dedicated to the legacy v1
warning admin-lte > tempusdominus-bootstrap-4 > popper.js@1.16.1: You can find the new Popper v2 at @popperjs/core, this package is dedicated to the legacy v1
warning admin-lte > pdfmake > pdfkit > fontkit > babel-runtime > core-js@2.6.11: core-js@<3 is no longer maintained and not recommended for usage due to the number of issues. Please, upgrade your dependencies to the actual version of core-js@3.
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning "admin-lte > bootstrap-switch@3.3.4" has incorrect peer dependency "bootstrap@^3.1.1".
warning "admin-lte > tempusdominus-bootstrap-4@5.39.0" has unmet peer dependency "moment-timezone@^0.5.31".
warning "admin-lte > tempusdominus-bootstrap-4@5.39.0" has unmet peer dependency "tempusdominus-core@5.19.0".
[4/4] 🔨  Building fresh packages...
success Saved lockfile.
warning Your current version of Yarn is out of date. The latest version is "1.22.5", while you're on "1.22.4".
info To upgrade, run the following command:
$ brew upgrade yarn
success Saved 165 new dependencies.
info Direct dependencies
└─ admin-lte@3.0.5
info All dependencies
├─ @fortawesome/fontawesome-free@5.15.1
├─ @fullcalendar/bootstrap@4.4.2
├─ @fullcalendar/core@4.4.2
├─ @fullcalendar/daygrid@4.4.2
├─ @fullcalendar/interaction@4.4.2
├─ @fullcalendar/timegrid@4.4.2
├─ @lgaitan/pace-progress@1.0.7
├─ @sweetalert2/theme-bootstrap-4@3.2.0
├─ @ttskch/select2-bootstrap4-theme@1.3.4
...
.....
├─ through@2.3.8
├─ toastr@2.1.4
├─ type@1.2.0
├─ typedarray@0.0.6
├─ unicode-properties@1.3.1
├─ unicode-trie@1.0.0
├─ universalify@1.0.0
├─ util-deprecate@1.0.2
├─ word-wrap@1.2.3
├─ xmldoc@1.1.2
└─ xtend@4.0.2
✨  Done in 27.20s.
```

### Userにroleを追加

```ruby
class AddRoleToUsers < ActiveRecord::Migration[5.2]
  def change
    add_column :users, :role, :integer, null: false, default: 0
  end
end
enumを設定
enum role: { general: 0, admin: 1 }
```

### AdminLTE3サンプルのHTMLで使用されているJavaScriptとCSSと同じものを適用すればいい。<br>
→検証ツールから確認して、パスを書く。

<img width="1084" alt="Admin LTEのソースコード" src="https://user-images.githubusercontent.com/60560627/99899343-ed978900-2ceb-11eb-9ebf-c8ca6fa01c8b.png">

<img width="1237" alt="スクリーンショット 2020-11-22 14 07 32" src="https://user-images.githubusercontent.com/60560627/99899324-c5a82580-2ceb-11eb-9f7f-b27bc2831c0e.png">

<img width="1241" alt="スクリーンショット 2020-11-22 14 07 55" src="https://user-images.githubusercontent.com/60560627/99899327-cc369d00-2ceb-11eb-94e7-423122a94935.png">

assetsに読み込むファイルを記述する。
```ruby
(app/assets/stylesheets/admin.scss)
@import 'admin-lte/plugins/fontawesome-free/css/all.min.css';
@import 'admin-lte/dist/css/adminlte.min.css';
@import 'https://code.ionicframework.com/ionicons/2.0.1/css/ionicons.min.css';
@import 'admin-lte/plugins/icheck-bootstrap/icheck-bootstrap.min.css';
```

```ruby
(app/assets/javascripts/admin.js)
//= require jquery3
//= require rails-ujs
//= require 'admin-lte/plugins/jquery/jquery.min.js'
//= require 'admin-lte/plugins/bootstrap/js/bootstrap.bundle.min.js'
//= require 'admin-lte/dist/js/adminlte.min.js'
```


```ruby
(app/views/layouts/admin_login.html.slim)
doctype html
html
  head
    meta content=("text/html; charset=UTF-8") http-equiv="Content-Type" /
    meta charset="utf-8" /
    = csrf_meta_tags
    = csp_meta_tag
    = stylesheet_link_tag 'admin', media: 'all'
    = javascript_include_tag 'admin'
  body
    div
      / = render 'shared/flash_message'
      = yield
```

### `config/initializers/assets.rb`の記述で、node_modulesを省略した記述が通る&プリコンパイルができる

```ruby
(config/initializers/assets.rb)

Rails.application.config.assets.paths << Rails.root.join('node_modules')

Rails.application.config.assets.precompile += ['admin.css', 'admin.js']
```

### ルーティングの記述
```ruby
namespace :admin do
  get 'login', to: 'user_sessions#new'
  post 'login', to: 'user_sessions#create'
  delete 'logout', to: 'user_sessions#destroy'
  resources :users
end
```

### Adminのコントローラの親となるAdmin::BaseControllerを作成

- ApplicationControllerを継承した形
- レイアウトファイルはlayouts/admin
- Userのroleでリダイレクト先を変える

```ruby
(app/controllers/admin/base_controller.rb)
class Admin::BaseController < ApplicationController
  before_action :require_login
  before_action :admin_authenticate
  layout 'layouts/admin'

  def not_authenticated
    flash[:warning] = 'ログインしてください'
    redirect_to admin_login_path
  end

  def admin_authenticate
    redirect_to root_path, danger: '権限がありません' unless current_user.admin?
  end
end
```

## ログイン機能

### AdminのコントローラはAdmin::BaseControllerを継承させる
- レイアウトファイルはlayouts/admin_login<br>
→ログインフォームだけ、ログイン後画面とレイアウトが異なるため、別のlayoutファイルを作成する

```ruby
(app/controllers/admin/user_sessions_controller.rb)
class Admin::UserSessionsController < Admin::BaseController
  skip_before_action :require_login, only: %i[new create]
  skip_before_action :admin_authenticate, only: %i[new create]
  layout 'layouts/admin_login'

  def new; end

  def create
    @user = login(params[:email], params[:password])
    if @user
      redirect_to admin_users_path, success: 'ログインに成功しました'
    else
      flash.now[:danger] = 'ログインに失敗しました'
      render :new
    end
  end

  def destroy
    logout
    redirect_to admin_login_path, success: 'ログアウトしました'
  end
end
```

### ログインページ用のレイアウトファイル

```ruby
(app/views/layouts/admin_login.html.slim)
doctype html
html
  head
    meta content=("text/html; charset=UTF-8") http-equiv="Content-Type" /
    meta charset="utf-8" /
    title InstaCloneApp
    = csrf_meta_tags
    = csp_meta_tag
    = stylesheet_link_tag 'admin', media: 'all'
    = javascript_include_tag 'admin'
  body
    = render 'shared/flash_messages'
    = yield
```

### ログインページ

```ruby
(app/views/admin/user_sessions/new.html.slim)
.login-logo
  a href="../../index2.html"  ログイン
.card
  .card-body.login-card-body
    = form_with url: admin_login_path, local: true do |f|
      .input-group.mb-3
        = f.label :email, 'メールアドレス'
        = f.text_field :email, class: "form-control", placeholder: "メールアドレス"
        .input-group-append
          .input-group-text
            span.fas.fa-envelope
      .input-group.mb-3
        = f.label :password, 'パスワード'
        = f.password_field :password, class: "form-control", placeholder: "パスワード"
        .input-group-append
          .input-group-text
            span.fas.fa-lock
      .row
        .col-4
          = f.submit 'ログイン', class: "btn btn-primary btn-block"
```

## ログイン後画面

### 管理画面ログイン後のコントローラ
レイアウトファイルはlayouts/admin

```ruby
(app/controllers/admin/users_controller.rb)
class Admin::UsersController < Admin::BaseController
  layout 'layouts/admin'

  def index
    @users = User.all
  end
end
```

### 管理画面ログイン後のレイアウトファイル
- ここでAdminLTEのhtmlをコピーして、各ページのビューファイルを作成する。
- sidebarなどのDBと関係無い箇所を、renderで部分テンプレートにする。
- yieldに各アクションから呼び出されたビューファイルが入る

```ruby
(app/views/layouts/admin.html.slim)
doctype html
html
  head
    meta content=("text/html; charset=UTF-8") http-equiv="Content-Type" /
    meta[name="viewport" content="width=device-width, initial-scale=1.0"]
    title InstaCloneApp
    = csrf_meta_tags
    = csp_meta_tag
    = stylesheet_link_tag 'admin', media: 'all'
    = javascript_include_tag 'admin'

  body.hold-transition.sidebar-mini
    div.wrapper
      / Navbar / 上部
      = render "admin/shared/navbar"
      / Main Sidebar Container 左のsidebar
      = render "admin/shared/main_sidebar"
      / yield部分に各コントローラーで表示するテンプレートが表示される。
      .content-wrapper
        = render "shared/flash_messages"
        = yield
      / Main Footer --
      = render "admin/shared/footer"
```

```ruby
(app/views/admin/shared/_navbar.html.slim)
nav.main-header.navbar.navbar-expand.navbar-white.navbar-light
    / Left navbar links
    ul.navbar-nav
      li.nav-item
        a.nav-link data-widget="pushmenu" href="#"
          i.fas.fa-bars

      li.nav-item.d-none.d-sm-inline-block
        a.nav-link href="index3.html"

      li.nav-item.d-none.d-sm-inline-block
        a.nav-link href="#"
    ul.navbar-nav
      li.nav-item
        = link_to admin_logout_path, class: "nav-link", method: :delete
          | ログアウト
```

```ruby
(app/views/admin/shared/_main_sidebar.html.slim)
aside.main-sidebar.sidebar-dark-primary.elevation-4
  / Brand Logo
  a href="index3.html" class='brand-link'
    span.brand-text.font-weight-light
      | AdminLTE 3

  / Sidebar
  .sidebar
    .user-panel.mt-3.pb-3.mb-3.d-flex
      .info
        a href="#" class="d-block"
          = current_user.username

    / Sidebar Menu
    nav.mt-2
      ul.nav.nav-pills.nav-sidebar.flex-column data-widget="treeview" role="menu" data-accordion="false"
        / ユーザーメニュー
        li.nav-item
          = link_to admin_users_path, class: "nav-link"
            i.nav-icon.far.fa-user
            | ユーザー
```

### ユーザー一覧機能

```ruby
(app/views/admin/users/index.html.slim)
table.table.table-striped
    thead
    tr
      th scope="col"
        | ID
      th scope="col"
        | ユーザー名
      th scope="col"
        | 権限
      th scope="col"
        | 作成日時

    = render @users
```

```ruby
(app/views/admin/users/_user.html.slim)
tbody
  tr
    th scope="row"
      = user.id
    td
      = user.username
    td
      = user.role_i18n
    td
      = l user.created_at, format: :long
```

### enum_roleの設定

```ruby
(config/locales/ja.yml)
ja:
  enums:
    user:
      role:
        general: '一般'
        admin: '管理者'
```
