# slack通知の設定

#### credentials
- [Rails5.2から追加された credentials.yml.enc のキホン](https://qiita.com/NaokiIshimura/items/2a179f2ab910992c4d39)  
- [秘匿情報はsecret.ymlではなくcredentials.yml.encで管理する【初心者】](https://qiita.com/15grmr/items/a687d0ed211ef60e751c)

#### Rack
- [Rackとは何か](https://qiita.com/k0kubun/items/248395f68164b52aec4a)

#### エラーハンドリング
- [Railsアプリの例外ハンドリングとエラーページの表示についてまとめてみた](https://qiita.com/upinetree/items/273ae574f1c021d24c37)  
- [Rails のエラー処理について知ってる範囲でまとめ](https://qiita.com/myt732/items/42a46dcd4943ca9c0c24)
- [rescue_fromの走査は下から上だった。](rescue_fromの走査は下から上だった。)

#### ログの出力レベル
- [【Ruby on Rails】ログ出力とログローテートの設定まとめ【Logger】](https://techblog.kyamanak.com/entry/2017/12/25/151921)


## チャンネルのWebhookURLを取得

## gemインストール

```ruby
gem 'slack-notifier'
gem 'exception_notification'
```

- 補足
trueに設定すると、どのような種類のエラーが発生しても、詳細なデバッグ情報がHTTPレスポンスに出力される。
developmentとtest環境ではtrue、production環境ではfalseに設定されている。

```ruby
config/environments/development.rb
# Show full error reports.
 # falseにするだけでdevelopment環境でエラーページの動作を確認できる
 config.consider_all_requests_local = false
```


## 例外エラーをslackチャンネルに通知するよう設定

`rails g exception_notification:install`

```terminal
(config/initializers/exception_notification.rb)

ExceptionNotification.configure do |config|
 config.add_notifier :slack, {
     webhook_url: Rails.application.credentials.dig(:slack, :webhook_url),
     channel: Settings.slack.exception_notification_channel
 }
end

   config.add_notifier :slack,
                       webhook_url: Rails.application.credentials.slack[:webhook_url],
                       channel: チャンネル名
```
 
- settingsファイルでチャンネル名設定

```ruby
(config/settings/development.yml)
slack:
 exception_notification_channel: '#基礎編通知'
```

#### 通知は500番エラー(サーバー側の問題)が発生した時だけ、通知が来るようになっている
→通知が来ることで、改修などを行える
→間違ったURLを入力した等の404エラー(クライアント側の問題)で毎回通知が来る必要ない


## credentialsファイルに秘匿情報(チャンネルURL)を保存しておく

- credentialsファイルをviで編集
$ EDITOR="vi" bin/rails credentials:edit

- credentialの中身

```
# aws:
#   access_key_id: 123
#   secret_access_key: 345
 
# Used as the base secret for all MessageVerifiers in Rails, including the one protecting cookies.
secret_key_base: da9bab9d8912dd3656a8453d06e56c2b748a45226b96c515855b977813add317f881c5943d9a33b32598e8b6ede59f9b645c06578231596e2492bdbb55c859a8
 
slack:
  webhook_url: https://hooks.slack.com/services/......
```

- 上記で入力した情報が、`config/credentials.yml.enc`に、暗号化された状態で保存される(リモート上で管理したくない秘匿情報)

```ruby:
(config/credentials.yml.enc)
TxvKX43N1GmClAAkxERwRumpqgrADiHG6qSY3udH7LLqm0/9kUV4ORBUkb+HIPQ+YL5UdMNaECxR2g1+2rhiHTQvz+7Dc0A25ZJqaxj0se2/cT7cAY1ueYyPloXbhSZFTf+vG4+2k+iLX9ga5EyDmmzHHgkZaoEZ3UvXSShlEpm4iOT2uNxNHKEen1QfZKt4JzzpYpqHtgeKSSN
```

- master_keyが情報の暗号化、複合を行ってくれる。

・マスターキーを保持していなければ、`EDITOR="vi" bin/rails credentials:edit`でマスターキーが作成される

```
$ bin/rails credentials:edit

Adding config/master.key to store the master encryption key: 0c314f2cb93a56c2....

Save this in a password manager your team can access.

If you lose the key, no one, including you, can access anything encrypted with it.

      create  config/master.key
```

- master_keyはバージョン管理対象外となっている

```
(.gitignore)
# Ignore master key for decrypting credentials and more.
/config/master.key
```

## 404、500エラーをハンドリングする。

- 404エラー時は、404エラー用のテンプレートを表示させる。
- 500エラー時は例外(エラー)クラス、エラーメッセージ 、バックトレースをログに出力させ、500エラー用のテンプレートを表示させる。

```ruby
(app/controllers/application_controller.rb)

rescue_from StandardError, with: :error500
 rescue_from ActiveRecord::RecordNotFound, with: :error404
 
def error500(error)
   logger.error("エラークラス: #{error.class}")
   logger.error("エラーメッセージ : #{error.message}")
   logger.error('バックトレース -------------')
   logger.error(error.backtrace.("\n"))
   logger.error('-------------')
   render file: Rails.root.join('public/500.html').to_s, status: :internal_server_error, layout: false, content_type: 'text/html'
 end
 
 def error404
   render file: Rails.root.join('public/404.html').to_s, status: :not_found, layout: false, content_type: 'text/html'
 end
 ```

- エラーログ

- 404エラー発生時のログ

```
(log/development.log)
Started GET "/boards/599" for ::1 at 2020-04-29 11:58:10 +0900
Processing by BoardsController#show as HTML
 Parameters: {"id"=>"599"}
 [1m[36mUser Load (2.5ms)[0m  [1m[34mSELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?[0m  [["id", 43], ["LIMIT", 1]]
 ↳ vendor/bundle/ruby/2.6.0/gems/activerecord-5.2.3/lib/active_record/log_subscriber.rb:98
 [1m[36mBoard Load (0.2ms)[0m  [1m[34mSELECT  "boards".* FROM "boards" WHERE "boards"."id" = ? LIMIT ?[0m  [["id", 599], ["LIMIT", 1]]
 ↳ app/controllers/boards_controller.rb:26
 Rendering public/404.html
 Rendered public/404.html (4.7ms)
Completed 404 Not Found in 61ms (Views: 16.7ms | ActiveRecord: 2.7ms)
```

- 500エラー発生時のログ

```
(log/development.log)
Started GET "/login" for ::1 at 2020-04-29 11:59:39 +0900
Processing by UserSessionsController#new as HTML
エラークラス: NameError
エラーメッセージ : undefined local variable or method `hello' for #<UserSessionsController:0x00007fd3cda92010>
バックトレース -------------
Completed 500 Internal Server Error in 219ms (ActiveRecord: 0.0ms)
 
 
 NoMethodError (undefined method `call' for #<Array:0x00007fd3c77ebf88>):
 app/controllers/application_controller.rb:20:in `error500'
 ```
