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
