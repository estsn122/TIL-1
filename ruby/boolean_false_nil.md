>>右辺の条件が成立する時に、左辺の式を評価してその結果を返します。 条件が成立しなければ nil を返します。

>>if 式は、条件が成立した節(あるいは else 節)の最後に評価し た式の結果を返します。else 節がなくいずれの条件も成り立たなけれ ば nil を返します。

- [【Ruby】後置ifが末尾にあるメソッドの返り値はなに...？](https://ysk-pro.hatenablog.com/entry/if-return-value)


```ruby
irb(main):054:1* def greeting(flag)
irb(main):055:1*   'good evening'
irb(main):056:2*   if flag
irb(main):057:2*     'good night'
irb(main):058:1*   end
irb(main):059:0> end
=> :greeting

irb(main):060:0> p greeting(false)
nil
=> nil

irb(main):061:0> p greeting("false")
"good night"
=> "good night"

irb(main):062:0> if false; end
=> nil
```
