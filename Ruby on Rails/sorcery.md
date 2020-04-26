[sorcery公式](https://github.com/Sorcery/sorcery)を参照にした。  
[sorceryメソッド](https://github.com/Sorcery/sorcery/blob/7c5fee441e03779ee2888ecef317440cd10a17e7/lib/sorcery/controller.rb#L76-L78)

[ActionMailer::Base < AbstractController::Base](https://api.rubyonrails.org/classes/ActionMailer/Base.html)
[Action Mailer の基礎/Railsガイド](https://railsguides.jp/action_mailer_basics.html)

## sorceryのインストール
```
gem 'sorcery'
```

## Userモデルを作成する
- `bundle exec rails g sorcery:install`を実行すると、以下が作成されるので、`db:migrate`する。 
  - initializerファイル(config/initializers/sorcery.rb）
  - Userモデル
  - migrationファイル(db/migrate/〇〇〇〇_sorcery_core.rb)

```ruby
(db/migrate/〇〇〇〇_sorcery_core.rb)
class SorceryCore < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :email,            null: false
      t.string :crypted_password
      t.string :salt
      t.string :last_name,         null: false
      t.string :first_name,        null: false

      t.timestamps                null: false
    end

    add_index :users, :email, unique: true
  end
end
```

```ruby
(schema.rb)
create_table "users", force: :cascade do |t|
    t.string   "email",            null: false
    t.string   "crypted_password"
    t.string   "salt"
    t.datetime "created_at",       null: false
    t.datetime "updated_at",       null: false
    t.index ["email"], name: "index_users_on_email", unique: true
end
```

```ruby
(app/models/user.rb)
class User < ApplicationRecord
  authenticates_with_sorcery!
end
```

```
rails g sorcery:install
```


- 暗号化されたパスワード(`crypted_password`)やソルト(`salt`)というカラムををユーザーに編集/閲覧させたくないため、
app/views/users/のあらゆるビューで使用してはいけない。

- 代わりにパスワードの「仮想的な」フィールドをビューに追加して、データベースに暗号化される前のパスワードを保持する。
  
```ruby
<div class="form-group">
  <%= f.label :password %>
  <%= f.password_field :password, class: "form-control" %>
</div>
<div class="form-group">
  <%= f.label :password_confirmation %>
  <%= f.password_field :password_confirmation, class: "form-control" %>
</div>
```

- Userモデルに以下のように記述することで、Userクラスがsorceryを使えるようになる。
  - `validates :password, confirmation: true`で、passwordというモデルのDBに存在しない仮想的な属性(virtual attributes)が追加される。
     - ユーザーが入力した暗号化されていない平文のpasswordはDBに保存したくないため、passwordカラムは作らないほうが良い。
  - `if: -> { new_record? || changes[:crypted_password] }`で、登録したユーザーがパスワード以外のプロフィール項目を更新したい場合に、パスワードの入力を省略させることができる。

```ruby
# app/models/user.rb
class User < ActiveRecord::Base
  authenticates_with_sorcery!

  validates :password, length: { minimum: 3 }, if: -> { new_record? || changes[:crypted_password] }
  validates :password, confirmation: true, if: -> { new_record? || changes[:crypted_password] }
  validates :password_confirmation, presence: true, if: -> { new_record? || changes[:crypted_password] }

  validates :email, uniqueness: true
end
```

- パスワードは自動的に暗号化され、ソルトも自動的に作成される。

## 認証機能

- ルーティング

```ruby
  get '/login', to: 'user_sessions#new', as: :login
  post '/login', to: 'user_sessions#create'
  delete 'logout', to: 'user_sessions#destroy', as: :logout
```

- require_loginメソッド
  - application_controller.rbに記述し共通化。
    - 各コントローラで`only`を使用し、アクションごとにログインを要求する。
- not_authenticatedメソッド
  - ログインできなかった(認証されなかった)場合に、このメソッドに記述した処理が行われる。

```ruby
(app/controllers/application_controller.rb)
class ApplicationController < ActionController::Base
  before_action :require_login

  private

  def not_authenticated
    redirect_to login_path, danger: t('defaults.please_login')
  end
end
```

- `redirect_back_or_to`メソッドで、
- `login`メソッドで、認証処理が行われる。
  - `@user = login(params[:email], params[:password])`でemailによるUser検索、パスワードの検証を行い、正常に処理できるとセッションデータにUserレコードのid値を格納する、という処理が行われる。
- `logout`メソッドで、セッションをリセットする。

```ruby
(app/controllers/user_sessions_controller.rb)
class UserSessionsController < ApplicationController
  skip_before_action :require_login, only: %i[create new]
  def new; end

  def create
    @user = login(params[:email], params[:password])
    if @user
      redirect_back_or_to boards_path, success: t('.succcessfully_logged_in')
    else
      flash.now[:danger] = t('.login_failed')
      render :new
    end
  end

  def destroy
    logout
    redirect_back_or_to root_path, success: t('.succcessfully_logged_out')
  end
end
```

```ruby
(app/views/user_sessions/new.html.erb)
<%= form_with url: login_path, local:true do |f| %>
  <div class="form-group">
    <%= f.label :email, User.human_attribute_name(:email) %>
    <%= f.text_field :email, class: "form-control" %>
  </div>
  <div class="form-group">
    <%= f.label :password, User.human_attribute_name(:password) %>
    <%= f.password_field :password, class: "form-control" %>
  </div>
  <div class="actions">
    <%= f.submit t('defaults.login'), class: "btn btn-primary" %>
  </div>
<% end %>
```

## ビューでログイン判定を使用し、表示するテンプレートを変えてみる。

```ruby
<% if logged_in? %>
  <%= render "shared/header" %>
  <% else %>
  <%= render "shared/before_login_header" %>
<% end %>
```

## sorceryが用意してくれているメソッド

```ruby
module InstanceMethods
  # To be used as before_action.
  # Will trigger auto-login attempts via the call to logged_in?
  # If all attempts to auto-login fail, the failure callback will be called.
  def require_login
    unless logged_in?
      session[:return_to_url] = request.url if Config.save_return_to_url && request.get? && !request.xhr?
      send(Config.not_authenticated_action)
    end
  end

  # Takes credentials and returns a user on successful authentication.
  # Runs hooks after login or failed login.
  def login(*credentials)
    @current_user = nil

    user_class.authenticate(*credentials) do |user, failure_reason|
      if failure_reason
        after_failed_login!(credentials)

        yield(user, failure_reason) if block_given?

        return
      end

      old_session = session.dup.to_hash
      reset_sorcery_session
      old_session.each_pair do |k, v|
        session[k.to_sym] = v
      end
      form_authenticity_token

      auto_login(user)
      after_login!(user, credentials)

      block_given? ? yield(current_user, nil) : current_user
    end
  end

def logout
  if logged_in?
    user = current_user
    before_logout!
    @current_user = nil
    reset_sorcery_session
    after_logout!(user)
  end
end

def logged_in?
  !!current_user
end

def current_user
  unless defined?(@current_user)
    @current_user = login_from_session || login_from_other_sources || nil
  end
  @current_user
end

# used when a user tries to access a page while logged out, is asked to login,
# and we want to return him back to the page he originally wanted.
def redirect_back_or_to(url, flash_hash = {})
  redirect_to(session[:return_to_url] || url, flash: flash_hash)
  session[:return_to_url] = nil
end

# The default action for denying non-authenticated users.
# You can override this method in your controllers,
# or provide a different method in the configuration.
def not_authenticated
  redirect_to root_path
end
```


# パスワードリセット

## パスワードリセット用のサブモジュールreset_passwordをインストール
- `rails g sorcery:install reset_password --only-submodules`を実行し、  
続いて`bundle exec rails db:migrate`を実行。

```terminal
$ rails g sorcery:install reset_password --only-submodules
Running via Spring preloader in process 99290
        gsub  config/initializers/sorcery.rb
      insert  app/models/user.rb
      create  db/migrate/20200419075054_sorcery_reset_password.rb
```     
      
```ruby
(db/migrate/2020○○○○○○_sorcery_reset_password.rb)
class SorceryResetPassword < ActiveRecord::Migration[5.2]
  def change
    add_column :users, :reset_password_token, :string, default: nil
    add_column :users, :reset_password_token_expires_at, :datetime, default: nil
    add_column :users, :reset_password_email_sent_at, :datetime, default: nil
    add_column :users, :access_count_to_reset_password_page, :integer, default: 0

    add_index :users, :reset_password_token
  end
end
```
      
```ruby
(schema.rb)
create_table "users", force: :cascade do |t|
        .
        .
    t.string "reset_password_token"
    t.datetime "reset_password_token_expires_at"
    t.datetime "reset_password_email_sent_at"
    t.integer "access_count_to_reset_password_page", default: 0
    t.index ["reset_password_token"], name: "index_users_on_reset_password_token"
   　　 .
    　　.
end
```

- password_reset_token にユニーク制約を付与しておく
  - パスワードを変更した際にreset_password_tokenがnilになるのでallow_nilが必要。
  - 同じ理屈でユーザーの新規作成もできなくなる
  
```ruby
(user.rb)
validates :reset_password_token, uniqueness: true, allow_nil: true
```


## パスワードリセットメール用のMailerを作成
- `rails g mailer UserMailer reset_password_email`を実行

```ruby
$ rails g mailer UserMailer reset_password_email
Running via Spring preloader in process 99411
      create  app/mailers/user_mailer.rb
      invoke  erb
      create    app/views/user_mailer
      create    app/views/user_mailer/reset_password_email.text.erb
      create    app/views/user_mailer/reset_password_email.html.erb
```

- sorceryの定義ファイルで、当該サブモジュールを使用することが自動記述された。
```ruby
(config/initializers/sorcery.rb)
Rails.application.config.sorcery.submodules = [:reset_password]
```

- パスワードリセットに使用するActionMailerとして、UserMailerを指定。
  - app/mailers/user_mailer.rbが呼び出される。
```ruby
(config/initializers/sorcery.rb)
Rails.application.config.sorcery.submodules = [:reset_password, blabla, blablu, ...]

Rails.application.config.sorcery.configure do |config|
  config.user_config do |user|
    user.reset_password_mailer = UserMailer
  end
end
```

- メーラーを編集し、アクションに'user'パラメータを追加する。sorceryはそれを新しいユーザーにパラメータとして送るからです。
 - メール内に表示させる情報やメールの送信先を設定。
 - `reset_password_email(user)`がアクションと似た動きをし、`user_mailer/reset_password_email.html.erb`メイラービューを呼び出す。
```ruby
# app/mailers/user_mailer.rb
class UserMailer < ApplicationMailer

  # メイラーはコントローラと似通っている
  # パスワードリセット用のメールを送信できるメソッド
  # reset_password_emailメソッドなので、reset_password_email.〇〇のビューがメールのフォーマットになる?
  # コントローラの場合と同様、メイラーのメソッド内で定義されたインスタンス変数はビューで使える。
  def reset_password_email(user)
    @user = User.find(user.id)
    @url  = edit_password_reset_url(@user.reset_password_token)
    mail(to: user.email,
         subject: 'パスワードリセット')
  end
end
```

- メイラービューの設定
 ```ruby
 (app/views/user_mailer/reset_password_email.html.erb)
 <h1><%= @user.decorate.full_name %>様</h1> 
<p>=====================================</p>

<p>パスワード再発行のご依頼を受け付けました。</p><br>

<p>こちらのリンクからパスワードの再発行を行ってください。</p>

<p><a href="<%= @url %>"><%= @url %></a></p>
<!--  <%= link_to @url, @url %>  -->
```

```ruby
(app/views/user_mailer/reset_password_email.text.erb)
<%= @user.decorate.full_name %>様
===========================================

パスワード再発行のご依頼を受け付けました。

こちらのリンクからパスワードの再発行を行ってください。
<%= @url %>
<!-- <%= link_to @url, @url %> -->
```




- コントローラ#アクションを`rails g controller PasswordResets create edit update`で追加。

```ruby
(app/controllers/password_resets_controller.rb)
class PasswordResetsController < ApplicationController
  skip_before_action :require_login

  def new; end

  # パスワードリセットをリクエストする
  # ユーザーがパスワードのリセットフォームにemailを入力し、送信したときに実行
  def create
    # form_withで送られてきたemailをparamsで受け取る
    @user = User.find_by(email: params[:email])
    # DBからデータを受け取れていれば、パスワードリセットの方法を記載したメールをユーザーに送信する（ランダムトークン付きのURL/有効期限付き）
    # @user.deliver_reset_password_instructions! if @user
    @user&.deliver_reset_password_instructions!
    # フォームに入力したemailがアプリ内に存在するか否かを問わず、成功メッセージを表示させる
    # これは、悪意ある攻撃者がDB内にそのemailが存在するかどうか分からなくするために行う。
    redirect_to login_path, success: t('.success')
  end

  # パスワードリセットフォームへ
  def edit
    # postされてきた値を取得
    @token = params[:id]
    # load_from_reset_password_tokenの簡単な説明
    # リクエストで送信されてきたトークンを使って、ユーザーの検索を行い, 有効期限のチェックも行う。
    # トークンが見つかり、有効であればそのユーザーオブジェクトを@userに格納
    @user = User.load_from_reset_password_token(params[:id])
    # @userがnilまたは空でないかをチェック
    return not_authenticated if @user.blank?
  end

  # ユーザーがパスワードのリセットフォームを送信したときに実行
  def update
    @token = params[:id]
    @user = User.load_from_reset_password_token(@token)
    return not_authenticated if @user.blank?
    # password_confirmation属性の有効性を確認
    @user.password_confirmation = params[:user][:password_confirmation]
    # change_passwordメソッドで、パスワードリセットに使用したトークンを削除し、パスワードを更新する
    if @user.change_password(params[:user][:password])
      redirect_to login_path, success: t('.success')
    else
      flash.now[:danger] = t('.fail')
      render :edit
    end
  end
end
```

- パスワードリセットを申請するフォーム 
 - 送信ボタンを押すと、createアクションへ
```ruby
(app/views/password_resets/new.html.erb)
<% content_for(:title, t('.title')) %>
<h1><%= t('.title') %></h1>
<%= form_with url: password_resets_path, local: true do |f| %>
  <div class="field">
    <%= f.label :email, t(User.human_attribute_name(:email)) %>
    <%= f.email_field :email %>
    <%= f.submit t('.submit'), class:'btn btn-primary' %>
  </div>
<% end %>
```

- パスワードリセット用のフォーム
```ruby
(app/views/password_resets/edit.html.erb)
<% content_for(:title, t('.title')) %>
<div class="container">
  <div class="row">
    <div class="col col-md-10 offset-md-1 col-lg-8 offset-lg-2">
      <h1><%= t('.title') %></h1>
      <%= form_with model: @user, url: password_reset_path(@token), local: true do |f| %>
        <%= render 'shared/error_messages', object: f.object %>
        <div class="form-group">
          <%= f.label :email %><br>
          <%= @user.email %>
        </div>
        <div class="form-group">
          <%= f.label :password %><br>
          <%= f.password_field :password, class: 'form-control' %>
        </div>
        <div class="form-group">
          <%= f.label :password_confirmation %><br>
          <%= f.password_field :password_confirmation, class: 'form-control' %>
        </div>
        <div class="actions">
          <p class="text-center">
            <%= f.submit class:'btn btn-primary' %>
          </p>
        </div>
      <% end %>
    </div>
  </div>
</div>
```
