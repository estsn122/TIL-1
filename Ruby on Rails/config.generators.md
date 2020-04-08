## config/application.rb内の設定によって、generate コマンドで生成されるファイルに制限をかける。

プロジェクトごとに設定を変えたり、自分好みのカスタマイズができる。

- 以下のファイルの生成を無効にする。
 - assetsファイル
 - testファイル
 - ルーティング

```config/application.rb
puts class Application < Rails::Application

 #以下のように、generateコマンド時に生成されるファイルに制限をかける
   config.generators do |g|
     g.assets  false
     g.test_framework    false
     g.skip_routes   true
   end
end
```
