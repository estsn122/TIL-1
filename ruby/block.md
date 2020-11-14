#### 以下を見た時に`map`を使えそうと直感的に判断する。

```ruby
food_labels.each do |food_label|
  @food_lists = Food.search_by_label(food_label)
end
```

#### 改善後

```ruby
@food_lists = food_labels.map do |food_label|
  Food.search_by_label(food_label)
end
```

#### 以下ももっと綺麗に書ける

```ruby
@meal_record = MealRecord.new(food_id: params[:food_id])
@meal_record.meal_record_pictures = ActiveStorage::Blob.find(session[:meal_picture_id]) if session[:meal_picture_id]
```

#### 改善後

```ruby
@meal_record = MealRecord.new(food_id: params[:food_id]) do |meal_record|
  meal_record.meal_record_pictures = ActiveStorage::Blob.find(session[:meal_picture_id]) if session[:meal_picture_id]
end
```
