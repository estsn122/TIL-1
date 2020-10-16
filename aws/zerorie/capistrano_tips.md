# Rails6でCapistranoを使う

`deploy:assets:precompile`でエラーが表示される。でもエラー文がヒントにならない展開
　

## Rails6でデプロイするなら、yarnのインストールを忘れないように

以下、解決方法。<br>
簡単にまとめるとコレ

- capistranoでyarnを使えるようにしてなかった<br>
→gem 'capistrano-yarn'を入れる & Capfileにrequire 'capistrano/yarn'を追加
- EC2にyarnがインストールされてないから落ちる<br>
→インストールしてあげる


Rails6ではprecompileの時点でyarnインストールまで行う(合ってる？)<br>
→capistranoでyarnを使えるようにする

```
(Gemfile)
group :development do
  .
  .
  .
  gem 'capistrano-yarn'
end
```

```
(Capfile)
require 'capistrano/yarn'
```


この状態でcapコマンドを打つとyarn;installで落ちた<br>
→`/usr/bin/env:`にyarnがありませんと言われてる<br>

```
~/workspace/app/zero_calorie
$ BRANCH=develop be cap production deploy
.
.
.
00:11 bundler:install
      The Gemfile's dependencies are satisfied, skipping installation

00:11 yarn:install
      01 yarn install --production
      01 /usr/bin/env:
      01 yarn
      01 : No such file or directory
      01
#<Thread:0x00007f9922999568 /Users/funesakisuke/workspace/app/zero_calorie/vendor/bundle/ruby/2.7.0/gems/sshkit-1.21.0/lib/sshkit/runners/parallel.rb:10 run> terminated with exception (report_on_exception is true):
Traceback (most recent call last):
	13: from /Users/funesakisuke/workspace/app/zero_calorie/vendor/bundle/ruby/2.7.0/gems/sshkit-1.21.0/lib/sshkit/runners/parallel.rb:12:in `block (2 levels) in execute'
	10: from /Users/funesakisuke/workspace/app/zero_calorie/vendor/bundle/ruby/2.7.0/gems/capistrano-yarn-2.0.2/lib/capistrano/tasks/yarn.rake:17:in `block (3 levels) in <top (required)>'
/Users/funesakisuke/workspace/app/zero_calorie/vendor/bundle/ruby/2.7.0/gems/sshkit-1.21.0/lib/sshkit/command.rb:97:in `exit_status=': yarn exit status: 127 (SSHKit::Command::Failed)
yarn stdout: Nothing written
yarn stderr: /usr/bin/env: yarn: No such file or directory
	1: from /Users/funesakisuke/workspace/app/zero_calorie/vendor/bundle/ruby/2.7.0/gems/sshkit-1.21.0/lib/sshkit/runners/parallel.rb:11:in `block (2 levels) in execute'
/Users/funesakisuke/workspace/app/zero_calorie/vendor/bundle/ruby/2.7.0/gems/sshkit-1.21.0/lib/sshkit/runners/parallel.rb:15:in `rescue in block (2 levels) in execute': Exception while executing as ryota@54.248.24.26: yarn exit status: 127 (SSHKit::Runner::ExecuteError)
yarn stdout: Nothing written
yarn stderr: /usr/bin/env: yarn: No such file or directory
(Backtrace restricted to imported tasks)
cap aborted!
SSHKit::Runner::ExecuteError: Exception while executing as ryota@54.248.24.26: yarn exit status: 127
yarn stdout: Nothing written
yarn stderr: /usr/bin/env: yarn: No such file or directory


Caused by:
SSHKit::Command::Failed: yarn exit status: 127
yarn stdout: Nothing written
yarn stderr: /usr/bin/env: yarn: No such file or directory
```

`/usr/bin/env`を見に行ってるから、システムグローバルにYarnをインストールする

```
[ryota@ip-10-0-11-228 20200926013822]$ nodebrew uninstall 14.11.0

[ryota@ip-10-0-11-228 20200926013822]$ nodebrew list
not installed

current: none
```

zshrcで不要な箇所をコメントアウト

```
# export PATH=/home/ryota/.nodebrew/current/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/ryota/.local/bin:/home/ryota/bin
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"

# if [ -f ~/.nvm/nvm.sh ]; then
#         . ~/.nvm/nvm.sh
# fi
```

```
[ryota@ip-10-0-11-228 20200926013822]$ curl --silent --location https://rpm.nodesource.com/setup_12.x | sudo bash -

## Installing the NodeSource Node.js 12.x repo...

## Inspecting system...

+ rpm -q --whatprovides redhat-release || rpm -q --whatprovides centos-release || rpm -q --whatprovides cloudlinux-release || rpm -q --whatprovides sl-release
+ uname -m

## Confirming "el7-x86_64" is supported...

+ curl -sLf -o /dev/null 'https://rpm.nodesource.com/pub_12.x/el/7/x86_64/nodesource-release-el7-1.noarch.rpm'

## Downloading release setup RPM...

+ mktemp
+ curl -sL -o '/tmp/tmp.6AuXLUbBTo' 'https://rpm.nodesource.com/pub_12.x/el/7/x86_64/nodesource-release-el7-1.noarch.rpm'

## Installing release setup RPM...

+ rpm -i --nosignature --force '/tmp/tmp.6AuXLUbBTo'

## Cleaning up...

+ rm -f '/tmp/tmp.6AuXLUbBTo'

## Checking for existing installations...

+ rpm -qa 'node|npm' | grep -v nodesource

## Run `sudo yum install -y nodejs` to install Node.js 12.x and npm.
## You may also need development tools to build native addons:
     sudo yum install gcc-c++ make
## To install the Yarn package manager, run:
     curl -sL https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
     sudo yum install yarn
```

上で指示されてるようにnode.js, npm, yarnをインストールするコマンドを入力

```
[ryota@ip-10-0-11-228 20200926013822]$ sudo yum install -y nodejs

読み込んだプラグイン:extras_suggestions, langpacks, priorities, update-motd
amzn2-core                                                                                                                                                                               | 3.7 kB  00:00:00
nodesource                                                                                                                                                                               | 2.5 kB  00:00:00
nodesource/x86_64/primary_db                                                                                                                                                             |  45 kB  00:00:00
依存性の解決をしています
--> トランザクションの確認を実行しています。
---> パッケージ nodejs.x86_64 2:12.18.4-1nodesource を インストール
--> 依存性解決を終了しました。

依存性を解決しました

================================================================================================================================================================================================================
 Package                                      アーキテクチャー                             バージョン                                                    リポジトリー                                      容量
================================================================================================================================================================================================================
インストール中:
 nodejs                                       x86_64                                       2:12.18.4-1nodesource                                         nodesource                                        22 M

トランザクションの要約
================================================================================================================================================================================================================
インストール  1 パッケージ

総ダウンロード容量: 22 M
インストール容量: 68 M
Downloading packages:
警告: /var/cache/yum/x86_64/2/nodesource/packages/nodejs-12.18.4-1nodesource.x86_64.rpm: ヘッダー V4 RSA/SHA512 Signature、鍵 ID 34fa74dd: NOKEY
nodejs-12.18.4-1nodesource.x86_64.rpm の公開鍵がインストールされていません
nodejs-12.18.4-1nodesource.x86_64.rpm                                                                                                                                                    |  22 MB  00:00:00
file:///etc/pki/rpm-gpg/NODESOURCE-GPG-SIGNING-KEY-EL から鍵を取得中です。
Importing GPG key 0x34FA74DD:
 Userid     : "NodeSource <gpg-rpm@nodesource.com>"
 Fingerprint: 2e55 207a 95d9 944b 0cc9 3261 5ddb e8d4 34fa 74dd
 Package    : nodesource-release-el7-1.noarch (installed)
 From       : /etc/pki/rpm-gpg/NODESOURCE-GPG-SIGNING-KEY-EL
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
警告: RPMDB は yum 以外で変更されました。
  インストール中          : 2:nodejs-12.18.4-1nodesource.x86_64                                                                                                                                             1/1
  検証中                  : 2:nodejs-12.18.4-1nodesource.x86_64                                                                                                                                             1/1

インストール:
  nodejs.x86_64 2:12.18.4-1nodesource

完了しました!
```

確認
```
[ryota@ip-10-0-11-228 20200926013822]$ which node
/usr/bin/node
[ryota@ip-10-0-11-228 20200926013822]$ which yarn
/usr/bin/which: no yarn in (/home/ryota/.rbenv/shims:/home/ryota/.rbenv/bin:/home/ryota/.nodebrew/current/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/ryota/.local/bin:/home/ryota/bin)
[ryota@ip-10-0-11-228
```


```
[ryota@ip-10-0-11-228 20200926013822]$ curl -sL https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
[yarn]
name=Yarn Repository
baseurl=https://dl.yarnpkg.com/rpm/
enabled=1
gpgcheck=1                                                                                                                                                                       
gpgkey=https://dl.yarnpkg.com/rpm/pubkey.gpg
```

```
[ryota@ip-10-0-11-228 20200926013822]$ sudo yum install yarn

読み込んだプラグイン:extras_suggestions, langpacks, priorities, update-motd
yarn                                                                                                                                                                                     | 2.9 kB  00:00:00
yarn/primary_db                                                                                                                                                                          |  22 kB  00:00:00
依存性の解決をしています
--> トランザクションの確認を実行しています。
---> パッケージ yarn.noarch 0:1.22.5-1 を インストール

依存性を解決しました

================================================================================================================================================================================================================
 Package                                         アーキテクチャー                                  バージョン                                             リポジトリー                                     容量
================================================================================================================================================================================================================
インストール中:
 yarn                                            noarch                                            1.22.5-1                                               yarn                                            1.2 M

トランザクションの要約
================================================================================================================================================================================================================
インストール  1 パッケージ

総ダウンロード容量: 1.2 M
インストール容量: 5.1 M
Is this ok [y/d/N]: y
Downloading packages:
警告: /var/cache/yum/x86_64/2/yarn/packages/yarn-1.22.5-1.noarch.rpm: ヘッダー V4 RSA/SHA512 Signature、鍵 ID 6963f07f: NOKEY
yarn-1.22.5-1.noarch.rpm の公開鍵がインストールされていません
yarn-1.22.5-1.noarch.rpm                                                                                                                                                                 | 1.2 MB  00:00:00
https://dl.yarnpkg.com/rpm/pubkey.gpg から鍵を取得中です。
Importing GPG key 0x6963F07F:
 Userid     : "Yarn RPM Packaging <yarn@dan.cx>"
 Fingerprint: 9a6f 73f3 4beb 7473 4d8c 6914 9cbb b558 6963 f07f
 From       : https://dl.yarnpkg.com/rpm/pubkey.gpg
上記の処理を行います。よろしいでしょうか？ [y/N]y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  インストール中          : yarn-1.22.5-1.noarch                                                                                                                                                            1/1
  検証中                  : yarn-1.22.5-1.noarch                                                                                                                                                            1/1

インストール:
  yarn.noarch 0:1.22.5-1

完了しました!
```

yarnが/usr/bin/に入った

```
[ryota@ip-10-0-11-228 20200926013822]$ which yarn
/usr/bin/yarn
```

nodeもローカルと同じメインバージョン12系が入ってる

```
[ryota@ip-10-0-11-228 20200926013822]$ node -v
-bash: /home/ryota/.nodebrew/current/bin/node: No such file or directory

[ryota@ip-10-0-11-228 20200926013822]$ nodebrew list
not installed

current: none

zshrcの設定を再度読み込む
[ryota@ip-10-0-11-228 20200926013822]$ source ~/.zshrc


[ryota@ip-10-0-11-228 20200926013822]$ node -v
v12.18.4
```

いざcapコマンドでデプロイ

```
~/workspace/app/zero_calorie
$ BRANCH=develop be cap production deploy
.
.
.
00:12 bundler:install
      The Gemfile's dependencies are satisfied, skipping installation
00:12 yarn:install
      01 yarn install --production
      01 yarn install v1.22.5
      01 warning package-lock.json found. Your project contains lock files generated by tools other than Yarn. It is advised not to mix package managers in order to avoid resolution inconsistencies caused by…
      01 [1/4] Resolving packages...
      01 [2/4] Fetching packages...
      01 info fsevents@2.1.3: The platform "linux" is incompatible with this module.
      01 info "fsevents@2.1.3" is an optional dependency and failed compatibility check. Excluding it from installation.
      01 info fsevents@1.2.13: The platform "linux" is incompatible with this module.
      01 info "fsevents@1.2.13" is an optional dependency and failed compatibility check. Excluding it from installation.
      01 [3/4] Linking dependencies...
      01 warning " > tempusdominus-bootstrap-4@5.1.2" has unmet peer dependency "moment-timezone@^0.5.11".
      01 warning " > tempusdominus-bootstrap-4@5.1.2" has unmet peer dependency "tempusdominus-core@5.0.3".
      01 warning " > webpack-dev-server@3.11.0" has unmet peer dependency "webpack@^4.0.0 || ^5.0.0".
      01 warning "webpack-dev-server > webpack-dev-middleware@3.7.2" has unmet peer dependency "webpack@^4.0.0".
      01 [4/4] Building fresh packages...
      01 Done in 12.10s.
    ✔ 01 ryota@54.248.24.26 12.532s
00:24 deploy:assets:precompile
      01 $HOME/.rbenv/bin/rbenv exec bundle exec rake assets:precompile
      01 yarn install v1.22.5
      01 warning package-lock.json found. Your project contains lock files generated by tools other than Yarn. It is advised not to mix package managers in order to avoid resolution inconsistencies caused by…
      01 [1/4] Resolving packages...
      01 [2/4] Fetching packages...
      .
      .
      .
      .
      01
      01 WARNING in webpack performance recommendations:
      01 You can limit the size of your bundles by using import() or require.ensure to lazy load some parts of your application.
      01 For more info visit https://webpack.js.org/guides/code-splitting/
      01 Child mini-css-extract-plugin node_modules/css-loader/dist/cjs.js??ref--6-1!node_modules/postcss-loader/src/index.js??ref--6-2!node_modules/sass-loader/dist/cjs.js??ref--6-3!app/javascript/styleshee…
      01     Entrypoint mini-css-extract-plugin = *
      01     [0] ./node_modules/css-loader/dist/cjs.js??ref--6-1!./node_modules/postcss-loader/src??ref--6-2!./node_modules/sass-loader/dist/cjs.js??ref--6-3!./app/javascript/stylesheets/application.scss 608…
      01         + 1 hidden module
      01


01:43 deploy:assets:backup_manifest
      01 mkdir -p /var/www/zero_calorie/releases/20200926023311/assets_manifest_backup
    ✔ 01 ryota@54.248.24.26 0.086s
      02 cp /var/www/zero_calorie/releases/20200926023311/public/assets/.sprockets-manifest-a4309c116324aec431521315715923d2.json /var/www/zero_calorie/releases/20200926023311/assets_manifest_backup
    ✔ 02 ryota@54.248.24.26 0.079s
01:44 deploy:db_create
      01 $HOME/.rbenv/bin/rbenv exec bundle exec rake db:create
      01 Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
      01 Couldn't create 'zero_calorie_production' database. Please check your configuration.
      01 rake aborted!
      01 Mysql2::Error::ConnectionError: Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
```

deploy:assets:precompile通った！！


## `Mysql2::Error::ConnectionError: Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)`のエラー

Mysql2::Error::ConnectionError: Can’t connect to local MySQL server through socket ‘/tmp/mysql.sock’ というエラー

ローカルのdatabase.ymlの設定
```
production:
  <<: *default
  database: zero_calorie_production
  adapter: mysql2
  encoding: utf8mb4
  charset: utf8mb4
  collation: utf8mb4_general_ci
  username: <%= Rails.application.credentials.dig(:database, :username) %>
  password: <%= Rails.application.credentials.dig(:database, :password) %>
  host: <%= Rails.application.credentials.dig(:database, :host) %>
  pool: 5
  timeout: 5000
  socket: /tmp/mysql.sock
```

EC2のsocketのパスに合わせる？
```
~/workspace/app/zero_calorie
$ mysql_config --socket 
/tmp/mysql.sock
```

```
[ryota@ip-10-0-11-228 20200926023311]$ mysql_config --socket
/var/lib/mysql/mysql.sock
```

database.ymlの記述を変える

```
production:
  <<: *default

socket: /var/lib/mysql/mysql.sock
```

つながらない
```
Mysql2::Error::ConnectionError: Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (13)
```

EC2にsshで入って、mysqlつなげられるかテストしてみる。

EC2にて、`-h`>オプションでhostを指定してMySQLにログインできるか確認する。<br>
接続はできているみたい。
```
[ryota@ip-10-0-11-228 20200926023311]$ mysql -u ryota -p -h zerorie-database-1.c24h1oi6hhez.ap-northeast-1.rds.amazonaws.com
Enter password:

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 1116
Server version: 5.7.22 Source distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]>
```
hostをcredentials使わずに直書きしてみたら、エラー内容変わった。
```
production:
  <<: *default
  database: zero_calorie_production
  username: <%= Rails.application.credentials.dig(:database, :username) %>
  password: <%= Rails.application.credentials.dig(:database, :password) %>
  host: zerorie-database-1.c24h1oi6hhez.ap-northeast-1.rds.amazonaws.com
  pool: 5
  timeout: 5000
  socket: /var/lib/mysql/mysql.sock
```

capコマンドのdb:create時のエラー<br>
パスワードなしでログインしようとして拒否されてる？
```
Mysql2::Error::ConnectionError: Access denied for user 'ryota'@'10.0.11.228' (using password: NO)
```

credentialsの問題っぽい！<br>
production環境用にcredentialsを用意してないから、 `using password: NO`になってそう。<br>
試しに`database.yml`にpasswordを直書き(pushしてない)したら、最後まで完了した。<br>
そしてブラウザでEC2のIPアドレスを入力すると、Railsのエラー画面が表示された。<br>


```
00:28 deploy:assets:precompile
      01 $HOME/.rbenv/bin/rbenv exec bundle exec rake assets:precompile
01:47 deploy:assets:backup_manifest
      01 mkdir -p /var/www/zero_calorie/releases/20200926045944/assets_manifest_backup
    ✔ 01 ryota@54.248.24.26 0.079s
      02 cp /var/www/zero_calorie/releases/20200926045944/public/assets/.sprockets-manifest-a4309c116324aec431521315715923d2.json /var/www/zero_calorie/releases/20200926045944/assets_manifest_backup
    ✔ 02 ryota@54.248.24.26 0.079s
01:48 deploy:db_create
      01 $HOME/.rbenv/bin/rbenv exec bundle exec rake db:create
      01 Database 'zero_calorie_production' already exists
    ✔ 01 ryota@54.248.24.26 2.133s
01:50 deploy:migrate
      [deploy:migrate] Run `rake db:migrate`
01:50 deploy:migrating
      01 $HOME/.rbenv/bin/rbenv exec bundle exec rake db:migrate
    ✔ 01 ryota@54.248.24.26 1.529s
01:52 deploy:symlink:release
      01 ln -s /var/www/zero_calorie/releases/20200926045944 /var/www/zero_calorie/releases/current
    ✔ 01 ryota@54.248.24.26 0.078s
      02 mv /var/www/zero_calorie/releases/current /var/www/zero_calorie
    ✔ 02 ryota@54.248.24.26 0.077s
01:52 nginx:restart
      01 sudo service nginx restart
      01 Redirecting to /bin/systemctl restart nginx.service
    ✔ 01 ryota@54.248.24.26 0.303s
01:52 deploy:cleanup
      Keeping 5 of 32 deployed releases on 54.248.24.26
      01 rm -rf /var/www/zero_calorie/releases/20200924155049 /var/www/zero_calorie/releases/20200924160307 /var/www/zero_calorie/releases/20200924160755 /var/www/zero_calorie/releases/20200924161532 /var/ww…
    ✔ 01 ryota@54.248.24.26 2.236s
01:55 deploy:log_revision
      01 echo "Branch develop (at 4282ecefbe61363dccbbb4b2fb27029001abbfba) deployed as release 20200926045944 by funesakisuke" >> /var/www/zero_calorie/revisions.log
    ✔ 01 ryota@54.248.24.26 0.105s
01:55 puma:make_dirs
      01 mkdir /var/www/zero_calorie/shared/tmp/sockets -p
    ✔ 01 ryota@54.248.24.26 0.083s
      02 mkdir /var/www/zero_calorie/shared/tmp/pids -p
    ✔ 02 ryota@54.248.24.26 0.081s
01:55 puma:start
      using conf file /var/www/zero_calorie/shared/puma.rb
      01 $HOME/.rbenv/bin/rbenv exec bundle exec puma -C /var/www/zero_calorie/shared/puma.rb --daemon
      01 Puma starting in single mode...
      01 * Version 4.3.5 (ruby 2.7.1-p83), codename: Mysterious Traveller
      01 * Min threads: 0, max threads: 16
      01 * Environment: production
      01 * Daemonizing...
    ✔ 01 ryota@54.248.24.26 0.701s
~/workspace/app/zero_calorie
```

`using password: NO`を解決したい。

EC2上に`etc/environment`に環境変数セットして、その環境変数を`database.yml`に記述したらイケた<br>
[デプロイ方法④(本番環境の設定/環境変数/Railsの起動) - Qiita](https://qiita.com/maru1124_/items/e83f07fbad5ebe4355d1)を参考にした。

```
ryota@ip-10-0-11-228 20200926052035]$ sudo vi /etc/environment
DATABASE_PASSWORD='MySQLのパスワード'
```

```
production:
  <<: *default
  database: zero_calorie_production
  username: <%= Rails.application.credentials.dig(:database, :username) %>
  password: <%= ENV['DATABASE_PASSWORD'] %>
  host: zerorie-database-1.c24h1oi6hhez.ap-northeast-1.rds.amazonaws.com
  pool: 5
  timeout: 5000
  socket: /var/lib/mysql/mysql.sock
ryota@ip-10-0-11-228 20200926052035]$ sudo vi /etc/environment
DATABASE_PASSWORD='MySQLのパスワード'
production:
  <<: *default
  database: zero_calorie_production
  username: <%= Rails.application.credentials.dig(:database, :username) %>
  password: <%= ENV['DATABASE_PASSWORD'] %>
  host: zerorie-database-1.c24h1oi6hhez.ap-northeast-1.rds.amazonaws.com
  pool: 5
  timeout: 5000
  socket: /var/lib/mysql/mysql.sock
```

しかし、これはとって来れてるのに
```
Rails.application.credentials.dig(:database, :username)
```
これはとってこれないのおかしくない？
```
Rails.application.credentials.dig(:database, :password)
```

envを設定して、rails consoleするとエラー。<br>
→なんとpush漏れしてただけ。。。

```
~/workspace/app/zero_calorie　　$ RAILS_ENV=production rails console
/Users/funesakisuke/workspace/app/zero_calorie/vendor/bundle/ruby/2.7.0/gems/aws-sigv4-1.2.1/lib/aws-sigv4/signer.rb:613:in `extract_credentials_provider': missing credentials, provide credentials with one of the following options: (Aws::Sigv4::Errors::MissingCredentialsError)
  - :access_key_id and :secret_access_key
  - :credentials
  - :credentials_provider
```

## pumaのエラー

アクセスすると`502 Bad Gateway
unix:/var/www/zero_calorie/shared/tmp/sockets/puma.sock failed (13: Permission denied) `

```
[ryota@ip-10-0-11-228 20200926071422]$ vi log/nginx.error.log
2020/09/26 07:24:32 [crit] 16201#0: *1 connect() to unix:/var/www/zero_calorie/shared/tmp/sockets/puma.sock failed (13: Permission denied) while connecting to upstream, client: 147.192.28.204, server: zero_calorie, request: "GET / HTTP/1.1", upstream: "http://unix:/var/www/zero_calorie/shared/tmp/sockets/puma.sock:/", host: "54.248.24.26"
```

### 以下解決
`/etc/conf.d/nginx.conf`の中のuserを`「root→ryota」`に変えたら画面表示された（デプロイ成功）

```
[ryota@ip-10-0-11-228 nginx]$ sudo vi nginx.conf 

/etc/conf.d/nginx.conf

# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

# root → ryotaに変更
user ryota;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
.
.
.
```

【原因】<br>
wwwだけ所有者がryotaになってる

```
[ryota@ip-10-0-11-228 var]$ ls -l
合計 8
drwxr-xr-x  2 root  root   19  9月  4 00:49 account
drwxr-xr-x  2 root  root    6  4月  9  2019 adm
drwxr-xr-x  6 root  root   63  9月  4 00:49 cache
drwxr-xr-x  3 root  root   18  9月  4 00:49 db
drwxr-xr-x  3 root  root   18  9月  4 00:49 empty
drwxr-xr-x  2 root  root    6  4月  9  2019 games
drwxr-xr-x  2 root  root    6  4月  9  2019 gopher
drwxr-xr-x  3 root  root   18  9月  4 00:49 kerberos
drwxr-xr-x 32 root  root 4096  9月 22 08:23 lib
drwxr-xr-x  2 root  root    6  4月  9  2019 local
lrwxrwxrwx  1 root  root   11  9月  4 00:48 lock -> ../run/lock
drwxr-xr-x  8 root  root 4096  9月 27 03:40 log
lrwxrwxrwx  1 root  root   10  9月  4 00:48 mail -> spool/mail
drwxr-xr-x  2 root  root    6  4月  9  2019 nis
drwxr-xr-x  2 root  root    6  4月  9  2019 opt
drwxr-xr-x  2 root  root    6  4月  9  2019 preserve
lrwxrwxrwx  1 root  root    6  9月  4 00:48 run -> ../run
drwxr-xr-x  9 root  root   97  9月  4 00:49 spool
drwxrwxrwt  5 root  root  186  9月 27 04:07 tmp
drwxr-xr-x  3 ryota root   26  9月 24 15:50 www
drwxr-xr-x  2 root  root    6  4月  9  2019 yp
```

capistrano使わずにデプロイしようとした時に、`www`を自作して、`sudo chown ryota www`してたのが問題やったのかも。このせいで権限エラーのおそれ。
```
[ryota@ip-10-0-11-228 var]$ sudo mkdir www
[ryota@ip-10-0-11-228 var]$ sudo chown ryota www
```

capistranoの中では、root権限で(?)mkdirしてくれる<br>
→sudo chownはしてない

```
(config/deploy.rb)

namespace :deploy do
  desc 'upload important files'
  task :upload do
    on roles(:app) do
      # capistranoがwwwディレクトリも作成してくれる(root)
      sudo :mkdir, '-p', '/var/www/zero_calorie/shared/config'
      sudo %(chown -R #{fetch(:user)}.#{fetch(:user)} /var/www/#{fetch(:application)})
      sudo :mkdir, '-p', '/etc/nginx/sites-enabled'
      sudo :mkdir, '-p', '/etc/nginx/sites-available'

      upload!('config/database.yml', '/var/www/zero_calorie/shared/config/database.yml')
      upload!('config/master.key', '/var/www/zero_calorie/shared/config/master.key')
    end
  end
```

>/etc/conf.d/nginx.confの中のuserを「root→ryota」に変えたら画面表示された

結局コレは`sudo chown root www`して、userをrootに戻した

### EC2でVision APIを叩く
ローカルではapp/gcp_key.jsonを読みたいけど、EC2上ではapp/shared/gcp_key.jsonを読みたい<br>
毎回修正するのはめんどすぎるし、credentialsは使えんかったはず。<br>
環境変数でパス読み込んどくか。capコマンド内で、EC2でのsource ~/.zshrcの実行できるんかね。

画像検索時
`The keyfile '/var/www/zero_calorie/releases/20200927075347/gcp_key.json' is not a valid file.`

該当コード
```
Google::Cloud::Vision.configure { |vision| vision.credentials = Rails.root.join('gcp_key.json').to_s }
```

APIキーである`gcp_key.json`はEC2の`/var/www/zero_calorie/shared/`配下に設置してるから、違うパスを読み込もうとしてる
```
[ryota@ip-10-0-11-228 20200927075347]$ RAILS_ENV=production bundle exec rails console

irb(main):001:0> Rails.root.join('../../shared/gcp_key.json').to_s
=> "/var/www/zero_calorie/shared/gcp_key.json"
irb(main):002:0> Rails.root.join('gcp_key.json').to_s
=> "/var/www/zero_calorie/releases/20200927075347/gcp_key.json"
```

EC2上でコード書き換えると動く
```
[ryota@ip-10-0-11-228 20200927075347]$ vi app/models/meal_picture.rb


Google::Cloud::Vision.configure { |vision| vision.credentials = Rails.root.join('../../shared/gcp_key.json').to_s }
```

