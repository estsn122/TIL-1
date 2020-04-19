[sorcery公式](https://github.com/Sorcery/sorcery)を参照にした。  
[sorceryメソッド](https://github.com/Sorcery/sorcery/blob/7c5fee441e03779ee2888ecef317440cd10a17e7/lib/sorcery/controller.rb#L76-L78)

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



- 暗号化されたパスワード(`crypted_password`)やソルト(`salt`)をユーザーに編集/閲覧させたくないため、
app/views/users/のすべてのビューで使用しないこと。

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
  - `validates :password, confirmation: true`で、仮想の属性が追加される。
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
