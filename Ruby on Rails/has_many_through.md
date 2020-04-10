### BoardモデルとUserモデルに、中間テーブルとの関連付を多対多関係よりも先に定義してあげることで、Userがお気に入りしてる掲示板を中間テーブル経由で取得できた。

```
(User.rb)
has_many :bookmarks, dependent: :destroy
has_many :bookmark_boards, through: :bookmarks, source: :board
```

```
[1] pry(main)> Bookmark.all
 Bookmark Load (2.9ms)  SELECT "bookmarks".* FROM "bookmarks"
=> [#<Bookmark:0x00007fc87e535540
 id: 12,
 user_id: 122,
 board_id: 87,
 created_at: Fri, 10 Apr 2020 22:10:45 JST +09:00,
 updated_at: Fri, 10 Apr 2020 22:10:45 JST +09:00>,
#<Bookmark:0x00007fc87e53d1a0
 id: 13,
 user_id: 122,
 board_id: 86,
 created_at: Fri, 10 Apr 2020 22:14:54 JST +09:00,
 updated_at: Fri, 10 Apr 2020 22:14:54 JST +09:00>]
[2] pry(main)> user = User.find(122)
 User Load (0.2ms)  SELECT  "users".* FROM "users" 
 WHERE "users"."id" = ? LIMIT ?  [["id", 122], ["LIMIT", 1]]
=> #<User:0x00007fc880dba790
id: 122,
email: "ryota1116funa@gmail.com",
crypted_password: "$2a$10$1ijf8eZSMn/QFF.yZslgUu8ygJXGStj544i2uwXyuAHczrEidNrTe",
salt: "Ze-f--a1Q1eKY_Rdt6sB",
last_name: "亮汰",
first_name: "船先",
created_at: Sun, 22 Mar 2020 10:29:51 JST +09:00,
updated_at: Tue, 07 Apr 2020 18:18:41 JST +09:00>
[3] pry(main)> user.bookmark_boards
 Board Load (0.2ms)  SELECT "boards".* FROM "boards" INNER JOIN "bookmarks" 
 ON "boards"."id" = "bookmarks"."board_id" WHERE "bookmarks"."user_id" = ?  [["user_id", 122]]
=> [#<Board:0x00007fc881a1b7a0
 id: 87,
 title: "ll",
 body: "だ\r\nだ",
 created_at: Wed, 08 Apr 2020 18:20:45 JST +09:00,
 updated_at: Wed, 08 Apr 2020 18:20:45 JST +09:00,
 user_id: 127,
 board_image: nil>,
#<Board:0x00007fc881a1b638
 id: 86,
 title: "💪",
 body: "がんばる\r\nのだ\r\n\r\nよ",
 created_at: Wed, 08 Apr 2020 12:26:24 JST +09:00,
 updated_at: Wed, 08 Apr 2020 18:35:21 JST +09:00,
 user_id: 127,
 board_image: nil>]
```
