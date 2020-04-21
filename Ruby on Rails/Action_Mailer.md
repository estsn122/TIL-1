[ActionMailer::Base < AbstractController::Base](https://api.rubyonrails.org/classes/ActionMailer/Base.html)  
[Action Mailer の基礎/Railsガイド](https://railsguides.jp/action_mailer_basics.html)



# アプリケーションのホスト情報をメイラー内で使う(動的処理)

```ruby
(config/settings/development.yml)
host: 'localhost:3000'
```

```ruby
(config/environments/development.rb)
  config.action_mailer.default_url_options = { host: Settings.host }
```

もしくは

```ruby
(config/settings/development.yml)
default_url_options:
  host: 'localhost:3000'
```

```ruby
(config/environments/development.rb)
  config.action_mailer.default_url_options = Settings.default_url_options.to_h
```











生成されたモデルはApplicationMailerを継承し、ActionMailer::Baseを継承します。
メーラーモデルは、メールメッセージを生成するために使用されるメソッドを定義します。
これらのメソッドでは、メーラーのビューで使用する変数、:fromアドレスなどのメール自体のオプション、添付ファイルを設定することができます。


Action Controllerと同様に、各メーラークラス(〇〇_mailer.rb)には対応するビューディレクトリがあり、その中で各メソッドがその名前のテンプレートを探します。

メーラーで使用するテンプレートを定義するには、メーラーモデルのメソッドと同じ名前の.erbファイルを作成します。例えば、上記で定義したメーラーでは、app/views/notifier_mailer/welcome.text.erb のテンプレートがメールを生成するために使用されます。

メーラーモデルのメソッドで定義された変数は、対応するビューのインスタンス変数としてアクセス可能です。

デフォルトではメールはプレーンテキストで送信されるので、モデル例のサンプルビューは以下のようになります。

ビュー内の件名、送信元、受信者にアクセスする必要がある場合は、メッセージ・オブジェクトを通してアクセスすることができます。



URLの生成

URL は、url_for または名前付きルートを使用してメーラー ビューで生成することができます。
Action Pack のコントローラとは異なり、メーラーのインスタンスは受信するリクエストに関するコンテキストを持たないので、URL を生成するために必要なすべての詳細を提供する必要があります。

url_forを使用する場合は、:host、:controller、および:actionを指定する必要があります。
<%= url_for(host: "example.com", controller: "welcome", action: "greeting") %> %>

名前付きルートを使用する場合は :host を指定するだけです。
<%= users_url(host: "example.com") %>

メールを読むクライアントは相対パスを決定するための現在のURLの概念を持たないので、named_route_urlスタイル(絶対URLを生成する)を使用し、named_route_pathスタイル(相対URLを生成する)の使用は避けるべきです。

⭐️config/application.rbの設定オプションとして:hostオプションを設定することで、すべてのメーラーで使用されるデフォルトホストを設定することも可能です。
config.action_mailer.default_url_options = { host: "example.com" }

また、個々のメーラーに対して default_url_options メソッドを定義して、メーラーごとにこれらのデフォルト設定を上書きすることもできます。

config.force_ssl が true の場合のデフォルトでは、ホスト用に生成された URL は HTTPS プロトコルを使用します。



そして、アプリに :letter_opener 配信メソッドが設定されていることを確認してください。
そして、メールを送信した後に http://localhost:3000/letter_opener にアクセスして楽しんでください。

Vagrantマシンからアプリを実行している場合は、letter_openerの起動時の呼び出しをスキップして、このようなメッセージを避けた方が良いかもしれません。

12:33:42 web.1 | Failure in opening /vagrant/tmp/letter_opener/1358825621_ba83a22/rich.html
をオプション {} で指定しています。ブラウザコマンドが見つかりません。これが予期しない場合は
環境変数 LAUNCHY_DEBUG=true または '-d' コマンドラインオプションを使用して、以下の場所にバグを報告してください。
https://github.com/copiousfreetime/launchy/issues/new

その場合(あるいはウェブインタフェースを使ってメールを閲覧したい場合)は、config/environments/development.rb で配送方法として :letter_opener_web を設定してください。

letter_opener_web を配送方法に設定している場合は、以下のようにイニシャライザに追加することで、文字の位置を変更することができます (あるいは development.rb に追加することもできます)。



www.DeepL.com/Translator（無料版）で翻訳しました。
