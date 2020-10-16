## Rails6でCapistranoを使う

### `deploy:assets:precompile`でエラーが表示される。でもエラー文がヒントにならない展開
　

### 以下、解決方法

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
