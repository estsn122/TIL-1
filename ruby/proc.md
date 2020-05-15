## `(%:method)`

`find_each(&:published!)`

```
# 公開待ち(publish_wait)の記事の中で、公開日時が過去になっているものがあれば、
# ステータスを「公開(published)」に変更する
Article.publish_wait.past_published.find_each(&:published!)
```

`Article.publish_wait`でenum値が`publish_wait`に該当するデータを取得

```
# Articleモデルのscope
# 公開日時が過去の日付になっている記事を取得
scope :past_publish, -> { where('published_at <= ?', Time.current) }
```

例えば10万件のレコードがあるとして、
→find_eachでオブジェクト(データ)を1000件ずつ取得
→(&:method)でオブジェクト一つ一つを呼び出して、published!メソッドを実行。

procとblockの理解が必要

`find_each(&:published!)`は以下と同じ
```
find_each do |object|
  object.published!
end
find_each(&:published)
```


do~end、 { |object| object.published! } を１行で書ける。
