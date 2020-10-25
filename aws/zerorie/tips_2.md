### 本番用のcredentialsを設定

```
rails credentials:edit --environment production
```

### 本番環境でrails console
```
RAILS_ENV=production rails console
```

 ### EC2で本番用DBにアクセスする
 
- [本番環境のdbをリセットする方法 - Qiita](https://qiita.com/potterqaz/items/ea6db5c5be2be389c0bb)
- [本番環境でRailsコンソールを実行する](https://qastack.jp/programming/15142752/running-rails-console-in-production)

```
[ryota@ip-10-0-11-228 zero_calorie]$ mysql -u ryota -p -h zerorie-database-1.c24h1oi6hhez.ap-northeast-1.rds.amazonaws.com
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 3048
Server version: 5.7.22 Source distribution
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
MySQL [(none)]> show databases;
+-------------------------+
| Database                |
+-------------------------+
| information_schema      |
| innodb                  |
| mysql                   |
| performance_schema      |
| sys                     |
| zero_calorie_production |
+-------------------------+
6 rows in set (0.01 sec)
MySQL [(none)]> use zero_calorie_production
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
Database changed
MySQL [zero_calorie_production]> show tables;
+-----------------------------------+
| Tables_in_zero_calorie_production |
+-----------------------------------+
| active_storage_attachments        |
| active_storage_blobs              |
| ar_internal_metadata              |
| authentications                   |
| food_food_genres                  |
| food_genres                       |
| foods                             |
| meal_pictures                     |
| meal_records                      |
| schema_migrations                 |
| users                             |
+-----------------------------------+
11 rows in set (0.00 sec)
MySQL [zero_calorie_production]> select * from users;
+----+----------------------------------------+--------------------------------------------------------------+----------------------+--------------------------------------------------------------+----------------------------+----------------------------+------+
| id | email                                  | crypted_password                                             | salt                 | name                                                         | created_at                 | updated_at                 | role |
+----+----------------------------------------+--------------------------------------------------------------+----------------------+--------------------------------------------------------------+----------------------------+----------------------------+------+
|  1 | date@example.com                       | $2a$10$1iY3lgC4ETIMTe32SlJBhOQAM.L9hdSrMhp2qk43C3GGW.pLLL9cu | j1ebZ2LoS55waiCzooDG | 伊達                                                         | 2020-09-27 04:44:18.328597 | 2020-09-27 04:44:18.328597 |    0 |
|  2 | user@example.com                       | $2a$10$1ea21oB/XQY8Tpi0m0KKO.WuQ69WIHDBGUIgHW8wCY/ToCrRnehb6 | sqcpQnNJhz66NUQGSuc_ | テストユーザー                                               | 2020-09-27 08:18:44.769767 | 2020-09-27 08:18:44.769767 |    0 |
|  3 | ahcees_dirdamlaer_atoyr@softbank.ne.jp | NULL                                                         | NULL                 | Ryota                                                        | 2020-10-02 08:29:56.320503 | 2020-10-02 08:29:56.320503 |    0 |
|  4 | ryota_chocolat                         | NULL                                                         | NULL                 | りょーた｜エンジニアを目指す金融マン✈︎                       | 2020-10-02 10:36:08.596089 | 2020-10-02 10:36:08.596089 |    0 |
+----+----------------------------------------+--------------------------------------------------------------+----------------------+--------------------------------------------------------------+----------------------------+----------------------------+------+
4 rows in set (0.00 sec)
MySQL [zero_calorie_production]>
```

sshキーの設定
```
$ ssh-add -l                            
The agent has no identities.

$ ssh-add -K ~/.ssh/id_rsa
Identity added: /Users/funesakisuke/.ssh/id_rsa (funesakisuke@ryota21.local)

$ ssh zero                                                                                                                                                                          【 feature/37_admin_page 】
Last login: Sun Oct 25 12:34:25 2020 from 210.232.14.172

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
3 package(s) needed for security, out of 15 available
Run "sudo yum update" to apply all updates.
[ryota@ip-10-0-11-228 ~]$
```
