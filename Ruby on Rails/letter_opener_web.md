#### 実際にメールを送信するのではなく、デフォルトのブラウザでメールをプレビューする。  
これにより、開発環境で実際にメールを送らずに済む。

```
group :development do
  gem 'letter_opener_web', '~> 1.0'
end
```

```ruby
(routes.rb)
mount LetterOpenerWeb::Engine, at: '/letter_opener' if Rails.env.development?
```

- `config/environments/development.rb`に配信方法を設定。
```ruby
(config/environments/development.rb)
  config.action_mailer.delivery_method = :letter_opener_web
  # アプリケーションのホスト情報をメイラー内で使う
  config.action_mailer.default_url_options = { host: Settings.host }
```

