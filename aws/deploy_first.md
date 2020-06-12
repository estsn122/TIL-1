
ルートユーザー
IAMユーザー

AWSで仮想的にネットワークやサーバーを構築する
（→どのリージョンの、どのアベイラビリティゾーンに置くのか）
Amazon VPC

その両域で自由なネットワークを作れる

EC2インスタンスというサービスでサーバーを作るz

インスタンスとは仮想的なサーバー




RDS

マスターユーザー名: ryota
マスターパスワード: 59~ryorta

- RDSの作成で、なぜサブネットにap-northeast-1aを選択するのか？(1cではなく)


EC2インスタンス

仮想サーバー
EC2インスタンスという単位で構築する

- EC2のサブネットにはパブリックサブネットを指定
→インターネットと接続するため

ここ設定する？





キーペアは、AWS が保存するパブリックキーとユーザーが保存するプライベートキーファイルで構成されます。
組み合わせて使用することで、インスタンスに安全に接続できます。
Windows AMI の場合、プライベートキーファイルは、インスタンスへのログインに使用されるパスワードを取得するために必要です。
Linux AMI の場合、プライベートキーファイルを使用してインスタンスに SSH で安全に接続できます。

~/.ssh/awsフォルダ内にaws-study-key.pemを保管
```
$ cd ~/.ssh

$ ls
id_rsa		id_rsa.pub	known_hosts

$ mkdir aws

$ ls
aws		id_rsa		id_rsa.pub	known_hosts

$ open .

$ ls
aws		id_rsa		id_rsa.pub	known_hosts

$ cd aws

$ ls
aws-study-key.pem
```



```
$ cd aws
~/.ssh/aws

$ ssh -i "aws-study-key.pem" ec2-user@52.192.237.51
Last login: Wed Jun 10 13:38:54 2020 from fp93c01ccc.tkyc208.ap.nuro.jp

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
4 package(s) needed for security, out of 8 available
Run "sudo yum update" to apply all updates.

[ec2-user@ip-10-0-11-136 ~]$ sudo yum update
...
.....
......
インストール:
  kernel.x86_64 0:4.14.181-140.257.amzn2

更新:
  amazon-linux-extras.noarch 0:1.6.11-1.amzn2       amazon-linux-extras-yum-plugin.noarch 0:1.6.11-1.amzn2       ca-certificates.noarch 0:2019.2.32-76.amzn2.0.2       cloud-init.noarch 0:19.3-3.amzn2
  python.x86_64 0:2.7.18-1.amzn2                    python-devel.x86_64 0:2.7.18-1.amzn2                         python-libs.x86_64 0:2.7.18-1.amzn2

完了しました!
```


sudo


terminal(シェル)にも、同じ仕組みが存在する。
ハードウェアになんらかの影響を与えるコマンド、パーティションの変更などストレージに重大な変更をくわえるコマンド、
ネットワーク設定を変更するコマンドなど、システム管理上重要な機能/役割を持つコマンドは軽々しく実行されては困るため、
なんらかのハードルを設ける必要がある。
その役割を果たすコマンドが「sudo」だ。



```
sudo adduser 新しいユーザー名
```

passwd ユーザー名
（指定したユーザーのパスワードを変更する


visudoコマンド
設定ファイルが編集できる

```
[ec2-user@ip-10-0-11-136 ~]$ man sudo
[ec2-user@ip-10-0-11-136 ~]$ sudo adduser ryota
[ec2-user@ip-10-0-11-136 ~]$ sudo passwd ryota
ユーザー ryota のパスワードを変更。
新しいパスワード:
よくないパスワード: このパスワードには一部に何らかの形でユーザー名が含まれています。
新しいパスワードを再入力してください:
passwd: すべての認証トークンが正しく更新できました。
[ec2-user@ip-10-0-11-136 ~]$ sudo visudo
[ec2-user@ip-10-0-11-136 ~]$ sudo visudo
visudo: /etc/sudoers.tmp は変更されません
[ec2-user@ip-10-0-11-136 ~]$ su - ryota
パスワード: 入力する

[ryota@ip-10-0-11-136 ~]$ sudo yum install \
> git make gcc-c++ patch \
> openssl-devel \
> libyaml-devel libffi-devel libicu-devel \
> libxml2 libxslt libxml2-devel libxslt-devel \
> zlib-devel readline-devel \
> mysql mysql-server mysql-devel \
> ImageMagick ImageMagick-devel \
> epel-release


[ryota@ip-10-0-11-136 ~]$ curl -L git.io/nodebrew | perl - setup
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0
  0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0
100 24634  100 24634    0     0  11866      0  0:00:02  0:00:02 --:--:--  289k
Fetching nodebrew...
Installed nodebrew in $HOME/.nodebrew

========================================
Export a path to nodebrew:

export PATH=$HOME/.nodebrew/current/bin:$PATH
========================================

[ryota@ip-10-0-11-136 ~]$ echo export PATH=$HOME/.nodebrew/current/bin:$PATH >> ~/.zshrc
[ryota@ip-10-0-11-136 ~]$ open ~/.zshrc
Couldn't get a file descriptor referring to the console
[ryota@ip-10-0-11-136 ~]$ source ~/.zshrc
[ryota@ip-10-0-11-136 ~]$ nodebrew install-binary latest
Fetching: https://nodejs.org/dist/v14.4.0/node-v14.4.0-linux-x64.tar.gz
######################################################################################################################################################################################################### 100.0%
Installed successfully
[ryota@ip-10-0-11-136 ~]$ nodebrew list
v14.4.0

current: none

[ryota@ip-10-0-11-136 ~]$ nodebrew use v14.4.0
use v14.4.0

[ryota@ip-10-0-11-136 ~]$ nodebrew list
v14.4.0

current: v14.4.0
```

---


```
[ryota@ip-10-0-11-136 ~]$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
```
→EC2上にいるから、普段の環境とは違う。だからzshrcの設定やってあげる必要ある？その必要はない？

```
[ryota@ip-10-0-11-136 ~]$ which rbenv
/usr/bin/which: no rbenv in (/home/ryota/.nodebrew/current/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/ryota/.local/bin:/home/ryota/bin)

rbenvのインストール
[ryota@ip-10-0-11-136 ~]$ git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
Cloning into '/home/ryota/.rbenv'...
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 2852 (delta 0), reused 1 (delta 0), pack-reused 2847
Receiving objects: 100% (2852/2852), 550.56 KiB | 866.00 KiB/s, done.
Resolving deltas: 100% (1781/1781), done.

[ryota@ip-10-0-11-136 ~]$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc

[ryota@ip-10-0-11-136 ~]$ ruby -v
-bash: ruby: command not found
[ryota@ip-10-0-11-136 ~]$ rbenv -v
-bash: rbenv: command not found

[ryota@ip-10-0-11-136 ~]$ echo 'eval "$(rbenv init -)"' >> ~/.zshrc
[ryota@ip-10-0-11-136 ~]$ open ~/.zshrc
Couldn't get a file descriptor referring to the console

[ryota@ip-10-0-11-136 ~]$ source ~/.zshrc

ruby-buildをインストール
[ryota@ip-10-0-11-136 ~]$ git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
Cloning into '/home/ryota/.rbenv/plugins/ruby-build'...
remote: Enumerating objects: 64, done.
remote: Counting objects: 100% (64/64), done.
remote: Compressing objects: 100% (47/47), done.
remote: Total 10959 (delta 31), reused 36 (delta 14), pack-reused 10895
Receiving objects: 100% (10959/10959), 2.31 MiB | 2.40 MiB/s, done.
Resolving deltas: 100% (7231/7231), done.

[ryota@ip-10-0-11-136 ~]$ rbenv -v
rbenv 1.1.2-30-gc879cb0
[ryota@ip-10-0-11-136 ~]$ ruby -v
-bash: ruby: command not found

[ryota@ip-10-0-11-136 ~]$ rbenv install -v 2.6.4
長い処理が実行される


[ryota@ip-10-0-11-136 ~]$ pwd
/home/ryota
[ryota@ip-10-0-11-136 ~]$ ruby -v
rbenv: ruby: command not found

The `ruby' command exists in these Ruby versions:
  2.6.4

[ryota@ip-10-0-11-136 ~]$ rbenv global 2.6.4
[ryota@ip-10-0-11-136 ~]$ ruby -v
ruby 2.6.4p104 (2019-08-28 revision 67798) [x86_64-linux]
[ryota@ip-10-0-11-136 ~]$ which rbenv
~/.rbenv/bin/rbenv
[ryota@ip-10-0-11-136 ~]$ cd ~/.rbenv/bin/rbenv
-bash: cd: /home/ryota/.rbenv/bin/rbenv: Not a directory
```
タイムアウトした
```
[ryota@ip-10-0-11-136 ~]$ OPpacket_write_wait: Connection to 52.192.237.51 port 22: Broken pipe

# 焦らず再接続
~/.ssh/aws
$ ssh -i "aws-study-key.pem" ec2-user@52.192.237.51
Last login: Wed Jun 10 14:06:03 2020 from fp93c01ccc.tkyc208.ap.nuro.jp

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[ec2-user@ip-10-0-11-136 ~]$ ruby -v
-bash: ruby: コマンドが見つかりません

ユーザーを変更
[ec2-user@ip-10-0-11-136 ~]$ su - ryota
パスワード:
最終ログイン: 2020/06/10 (水) 14:26:22 UTC日時 pts/0
[ryota@ip-10-0-11-136 ~]$ rbenv -v
-bash: rbenv: command not found
[ryota@ip-10-0-11-136 ~]$ ruby -v
-bash: ruby: command not found

[ryota@ip-10-0-11-136 ~]$ source ~/.zshrc
[ryota@ip-10-0-11-136 ~]$ rbenv -v
rbenv 1.1.2-30-gc879cb0
[ryota@ip-10-0-11-136 ~]$ ruby -v
ruby 2.6.4p104 (2019-08-28 revision 67798) [x86_64-linux]
```

これわからん
```
※ which rbenvを実行して、表示される相対パスからrbenvのインストール先に移動し、
  pwdコマンドで絶対パスを確認すると個別のユーザー毎のディレクトリにインストールされていることが分かります。
```
代わりにrbenv versionsで分かった気がするが
```
[ryota@ip-10-0-11-136 ~]$ which rbenv
~/.rbenv/bin/rbenv
[ryota@ip-10-0-11-136 ~]$ rbenv versions
* 2.6.4 (set by /home/ryota/.rbenv/version)
```


```
[ryota@ip-10-0-11-136 ~]$ ls -a
.  ..  .bash_history  .bash_logout  .bash_profile  .bashrc  .nodebrew  .rbenv  .ssh  .zshrc
[ryota@ip-10-0-11-136 ~]$ chmod 700 .ssh
[ryota@ip-10-0-11-136 ~]$ cd .ssh

[ryota@ip-10-0-11-136 .ssh]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ryota/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ryota/.ssh/id_rsa.
Your public key has been saved in /home/ryota/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:+YRbUfXNUus5pKqVeOquwyS35EK2+eH2+jFdLJDXDGE ryota@ip-10-0-11-136.ap-northeast-1.compute.internal
The key's randomart image is:
+---[RSA 2048]----+
|           Eo.. .|
|          o.+  +o|
|         o.. o.o+|
|         oo.. +..|
|        S o. + + |
|     + + =o =   .|
|    o X.o+.*     |
|     +.*. B      |
|      +=OB       |
+----[SHA256]-----+
```

```
ls
id_rsa  id_rsa.pub
[ryota@ip-10-0-11-136 .ssh]$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EDqIhF3nmFGywfYSXLQoyu8D5Lvl○○○○○○○○tR5Jb8Ud/sIzzS1DJ7S8F/RNKfdw+pxBimWmMV1CibmuR8xGQB4cwD+bd3R9Po62q35ZPRAxSITQsyfpZ03WmvjIPivQgIl+mKNac7Bu+1ToeGOajeXMN5rLSGUgUMAaYzdLxxYAW6DkgndQjC/98w8lZ1MMj96KImonk6o7rNRQzsGi+tJxP8bS8XZCKv ryota@ip-10-0-11-136.ap-northeast-1.compute.internal
```



varファイルにアクセスできるグループを追加？
```
sudo chown ryota var
```

```
sudo chown ryota var
[ryota@ip-10-0-11-136 /]$ ls -a
.  ..  .autorelabel  bin  boot  dev  etc  home  lib  lib64  local  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[ryota@ip-10-0-11-136 /]$ cd var
[ryota@ip-10-0-11-136 var]$ ls
account  adm  cache  db  empty  games  gopher  kerberos  lib  local  lock  log  mail  nis  opt  preserve  run  spool  tmp  yp
[ryota@ip-10-0-11-136 var]$ sudo mkdir www
[ryota@ip-10-0-11-136 var]$ ls
account  adm  cache  db  empty  games  gopher  kerberos  lib  local  lock  log  mail  nis  opt  preserve  run  spool  tmp  www  yp
[ryota@ip-10-0-11-136 var]$ sudo chown ryota www
[ryota@ip-10-0-11-136 var]$ cd www
[ryota@ip-10-0-11-136 www]$ ls
[ryota@ip-10-0-11-136 www]$ ls -a
.  ..
[ryota@ip-10-0-11-136 www]$ git clone https://github.com/ryota1116/runteq_advanced.git
Cloning into 'runteq_advanced'...
Username for 'https://github.com': ryota1116
Password for 'https://ryota1116@github.com':
remote: Enumerating objects: 1418, done.
remote: Counting objects: 100% (1418/1418), done.
remote: Compressing objects: 100% (635/635), done.
remote: Total 1418 (delta 845), reused 1297 (delta 725), pack-reused 0
Receiving objects: 100% (1418/1418), 3.14 MiB | 3.07 MiB/s, done.
Resolving deltas: 100% (845/845), done.

[ryota@ip-10-0-11-136 www]$ ls
runteq_advanced
```

本番用のアプリの設定


```
ローカルでmaster.keyを作成
$ rails credentials:edit                                                                                                                                                                           【 master 】
No $EDITOR to open file in. Assign one like this:

EDITOR="mate --wait" bin/rails credentials:edit

For editors that fork and exit immediately, it's important to pass a wait flag,
otherwise the credentials will be saved immediately with no chance to edit.

zshrcに`export EDITOR=“vim”`を書いて実行
$ vim ~/.zshrc                                                                                                                                                                     
$ source ~/.zshrc                                                                                                                                                                              
$ rails credentials:edit                                                                                                                                     

Adding config/master.key to store the master encryption key: b5dd832bb74d77d9d6ce9c0422feb8c7

Save this in a password manager your team can access.

If you lose the key, no one, including you, can access anything encrypted with it.

      create  config/master.key

Ignoring config/master.key so it won't end up in Git history:

      append  .gitignore

New credentials encrypted and saved.
~/workspace/runteq/332_ryota1116_runteq_learning_advanced
$ cd config                                                                                                                                                                            【 master 】【 master 】
~/.../332_ryota1116_runteq_learning_advanced/config
$ ls                                                                                                                                                                                   【 master 】【 master 】
application.rb			database.ci.yml			environment.rb			puma.rb				secrets.yml			webpacker.yml
boot.rb				database.yml			environments			rails_best_practices.yml	settings.yml
breadcrumbs.rb			database.yml.default		initializers			resque_schedule.yml		spring.rb
cable.yml			deploy				locales				routes.rb			storage.yml
credentials.yml.enc		deploy.rb			master.key			schedule.rb			webpack
EC2の中にmaster.key作成してローカルの値をコピペ
[ryota@ip-10-0-11-136 runteq_advanced]$ ls
app              bin      config       config.ru  entity_relation  Gemfile.lock  log           package-lock.json  public    README.md   spec
babel.config.js  Capfile  config.reek  db         Gemfile          lib           package.json  postcss.config.js  Rakefile  sideci.yml  yarn.lock
[ryota@ip-10-0-11-136 runteq_advanced]$ vim master.key
[ryota@ip-10-0-11-136 runteq_advanced]$ ls
app              bin      config       config.ru  entity_relation  Gemfile.lock  log         package.json       postcss.config.js  Rakefile   sideci.yml  yarn.lock
babel.config.js  Capfile  config.reek  db         Gemfile          lib           master.key  package-lock.json  public             README.md  spec
```

```
[ryota@ip-10-0-11-136 config]$ vim database.yml


default: &default
  adapter: mysql2
  encoding: utf8mb4
  charset: utf8mb4
  collation: utf8mb4_general_ci
  username: ryota
  password: 59272744ryota
  host: database-1.c24h1oi6hhez.ap-northeast-1.rds.amazonaws.com  # RDSのエンドポイント
  pool: 5
  timeout: 5000

  production:
          <<: *default
          database: aws-study-runteq-production
```



```
```
2.6.5がなかったからインストール
[ryota@ip-10-0-11-136 runteq_advanced]$ bundle install
rbenv: version `2.6.5' is not installed (set by /var/www/runteq_advanced/.ruby-version)

## ホームディレクトリに戻ってver.2.6.5をインストール
[ryota@ip-10-0-11-136 ~]$ rbenv install -v 2.6.5

[ryota@ip-10-0-11-136 .ssh]$ rbenv versions
* 2.6.4 (set by /home/ryota/.rbenv/version)
  2.6.5
[ryota@ip-10-0-11-136 .ssh]$ rbenv global 2.6.5
[ryota@ip-10-0-11-136 .ssh]$ rbenv versions
  2.6.4
* 2.6.5 (set by /home/ryota/.rbenv/version)
[ryota@ip-10-0-11-136 .ssh]$ rbenv rehash
[ryota@ip-10-0-11-136 .ssh]$ ruby -v
ruby 2.6.5p114 (2019-10-01 revision 67812) [x86_64-linux]
```

```
[ryota@ip-10-0-11-136 runteq_advanced]$ bundle install
Traceback (most recent call last):
	2: from /home/ryota/.rbenv/versions/2.6.5/bin/bundle:23:in `<main>'
	1: from /home/ryota/.rbenv/versions/2.6.5/lib/ruby/2.6.0/rubygems.rb:302:in `activate_bin_path'
/home/ryota/.rbenv/versions/2.6.5/lib/ruby/2.6.0/rubygems.rb:283:in `find_spec_for_exe': Could not find 'bundler' (2.0.2) required by your /var/www/runteq_advanced/Gemfile.lock. (Gem::GemNotFoundException)
To update to the latest version installed on your system, run `bundle update --bundler`.
To install the missing version, run `gem install bundler:2.0.2`

※ここでエラーとなりファイルの編集やgemの追加が必要になった場合は、EC2上で作業せずにローカルで作業してpushしたものをEC2上でpullしてください。
（リポジトリを更新しないと別環境でデプロイした際にエラーが再発するため）
この通りしたらいいのか
```

```
なんかエラー
$ bundle update --bundler
You must use Bundler 2 or greater with this lockfile.
```

```
EC2上のbundlerのバージョンが2じゃないからエラーになってるぽい
[ryota@ip-10-0-11-136 runteq_advanced]$ gem list bundler

*** LOCAL GEMS ***

bundler (default: 1.17.2)

$ gem list bundler                                                                                                                                                                     【 master 】【 master 】

*** LOCAL GEMS ***

bundler (2.1.4, default: 1.17.2)
capistrano-bundler (1.6.0)
```

```
[ryota@ip-10-0-11-136 runteq_advanced]$ gem install bundler:2.0.2
Fetching bundler-2.0.2.gem
Successfully installed bundler-2.0.2
Parsing documentation for bundler-2.0.2
Installing ri documentation for bundler-2.0.2
Done installing documentation for bundler after 3 seconds
1 gem installed

[ryota@ip-10-0-11-136 runteq_advanced]$ gem list bundler

*** LOCAL GEMS ***

bundler (2.0.2, default: 1.17.2)
[ryota@ip-10-0-11-136 runteq_advanced]$ bundler -v
Bundler version 2.0.2
```

```
[ryota@ip-10-0-11-136 runteq_advanced]$ bundle install
...
.....
An error occurred while installing sqlite3 (1.4.2), and Bundler cannot continue.
...
.....
Make sure that `gem install sqlite3 -v '1.4.2' --source 'https://rubygems.org/'` succeeds before bundling.
```
```


```
gem install sqlite3 -v '1.4.2' --source 'https://rubygems.org/'


bundle install

ok

[ryota@ip-10-0-11-136 runteq_advanced]$ rails db:create RAILS_ENV=production
rails aborted!
ArgumentError: Missing `secret_key_base` for 'production' environment, set this string with `rails credentials:edit`
/var/www/runteq_advanced/config/environment.rb:5:in `<top (required)>'
bin/rails:4:in `require'
bin/rails:4:in `<main>'
Tasks: TOP => db:create => db:load_config => environment
(See full trace by running task with --trace)

EC2に再度pullした時に、Git管理外にあるmaster.keyが消えたから、作り直してローカルと同じ値を入れる
[ryota@ip-10-0-11-136 config]$ vim master.key

[ryota@ip-10-0-11-136 config]$ cd ..
[ryota@ip-10-0-11-136 runteq_advanced]$ rails db:create RAILS_ENV=production
Created database 'aws-study-runteq-production'

[ryota@ip-10-0-11-136 runteq_advanced]$ rails db:migrate RAILS_ENV=production
== 20171230001508 CreateActiveStorageTables: migrating ========================
-- create_table(:active_storage_blobs)
   -> 0.0173s
```


nginx

webサーバー
```
[ryota@ip-10-0-11-136 runteq_advanced]$ sudo amazon-linux-extras install nginx1.12
NOTE: The livepatch extra is in public preview, not meant for production use

Topic nginx1.12 has end-of-support date of 2019-09-20
Installing nginx
...
.....


[ryota@ip-10-0-11-136 runteq_advanced]$ sudo service nginx start
Redirecting to /bin/systemctl start nginx.service
```

```
IPv4 パブリック IP : 52.192.237.51
```


nginxの設定ファイルを作成する

```
[ryota@ip-10-0-11-136 runteq_advanced]$ cd /etc/nginx/conf.d
[ryota@ip-10-0-11-136 conf.d]$ sudo vim 332_ryota1116_runteq_learning_advanced.conf
```

puma

アプリケーションサーバー

```
[ryota@ip-10-0-11-136 config]$ mkdir puma
[ryota@ip-10-0-11-136 config]$ cd puma/

[ryota@ip-10-0-11-136 puma]$ vim production.rb


[ryota@ip-10-0-11-136 runteq_advanced]$ bundle exec puma -C config/puma/production.rb -e production -d
[13153] Puma starting in cluster mode...
[13153] * Version 4.3.1 (ruby 2.6.5-p114), codename: Mysterious Traveller
[13153] * Min threads: 3, max threads: 3
[13153] * Environment: production
[13153] * Process workers: 2
[13153] * Preloading application
bundler: failed to load command: puma (/home/ryota/.rbenv/versions/2.6.5/bin/puma)
Errno::ENOENT: No such file or directory - connect(2) for /var/www/runteq_advanced/tmp/sockets/puma.sock
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/binder.rb:327:in `initialize'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/binder.rb:327:in `new'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/binder.rb:327:in `add_unix_listener'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/binder.rb:151:in `block in parse'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/binder.rb:90:in `each'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/binder.rb:90:in `parse'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/runner.rb:161:in `load_and_bind'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/cluster.rb:413:in `run'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/launcher.rb:172:in `run'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/lib/puma/cli.rb:80:in `run'
  /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/puma-4.3.1/bin/puma:10:in `<top (required)>'
  /home/ryota/.rbenv/versions/2.6.5/bin/puma:23:in `load'
  /home/ryota/.rbenv/versions/2.6.5/bin/puma:23:in `<top (required)>'


# 自力でtmp/socketsディレクトリを作る
[ryota@ip-10-0-11-136 runteq_advanced]$ mkdir tmp
[ryota@ip-10-0-11-136 runteq_advanced]$ cd tmp/
[ryota@ip-10-0-11-136 tmp]$ mkdir sockets

[ryota@ip-10-0-11-136 runteq_advanced]$ bundle exec puma -C config/puma/production.rb -e production -d
[13479] Puma starting in cluster mode...
[13479] * Version 4.3.1 (ruby 2.6.5-p114), codename: Mysterious Traveller
[13479] * Min threads: 3, max threads: 3
[13479] * Environment: production
[13479] * Process workers: 2
[13479] * Preloading application
[13479] * Listening on unix:///var/www/runteq_advanced/tmp/sockets/puma.sock
[13479] ! WARNING: Detected 1 Thread(s) started in app boot:
[13479] ! #<Thread:0x0000000003c102c0@/home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/connection_adapters/abstract/connection_pool.rb:299 sleep> - /home/ryota/.rbenv/versions/2.6.5/lib/ruby/gems/2.6.0/gems/activerecord-5.2.3/lib/active_record/connection_adapters/abstract/connection_pool.rb:301:in `sleep'
[13479] * Daemonizing...
```

```
[ryota@ip-10-0-11-136 runteq_advanced]$ ps -ax | grep nginx
12602 ?        Ss     0:00 nginx: master process /usr/sbin/nginx
12603 ?        S      0:00 nginx: worker process
13523 pts/0    S+     0:00 grep --color=auto nginx

[ryota@ip-10-0-11-136 runteq_advanced]$ sudo nginx -s stop

[ryota@ip-10-0-11-136 runteq_advanced]$ ps -ax | grep nginx
13531 pts/0    S+     0:00 grep --color=auto nginx

[ryota@ip-10-0-11-136 runteq_advanced]$ sudo service nginx start
Redirecting to /bin/systemctl start nginx.service
```




