## selfとattr_accessor - ローカル・インスタンス変数

- [Ruby - 【Rails】selfと@の使い分けはどうすればいいでしょうか？｜teratail](https://teratail.com/questions/202474)
- [rubyでselfを省略できる時、できない時 - Qiita](https://qiita.com/akira-hamada/items/4132d2fda7e420073ab7)
- [インスタンス変数 - クラスの概念 - Ruby入門](https://www.javadrive.jp/ruby/class/index4.html)

>>オブジェクトの中で値を保存しておくための利用されるのがインスタンス変数です
ローカル変数をメソッド内で使用することも可能です。ただしローカル変数は一度メソッドを抜けてしまえば値は消えてしまいます。
インスタンス変数はクラス内で全メソッドで共通して使用することが出来ます。




`attr_accessor`でインスタンス変数を読み書きするためのアクセサメソッドを定義している。 <br>
→インスタンス変数・・・`self.変数`＝`@変数`<br>
→@nameとした場合は、attr_accessorが裏で保管しているインスタンス変数を読みに行くみたい。


インスタンス変数に代入されるのは456の方。

```ruby
irb(main):037:1* class Hoge
irb(main):038:1*   attr_accessor :fuga
irb(main):039:1*
irb(main):040:2*   def foo
irb(main):045:2*     fuga = 123
irb(main):041:2*     self.fuga = 456
irb(main):042:1*   end
irb(main):047:0> end
```

「インスタンス変数に代入して返すか、ローカル変数に代入して返しているか」の違いか

```ruby
irb(main):037:1* class Hoge
irb(main):038:1*   attr_accessor :fuga
irb(main):039:1*
irb(main):040:2*   def foo
irb(main):041:2*     self.fuga = 123
irb(main):042:1*   end
irb(main):043:1*
irb(main):044:2*   def bar
irb(main):045:2*     fuga = 123
irb(main):046:1*   end
irb(main):047:0> end
=> :bar

irb(main):048:0> hoge = Hoge.new
irb(main):049:0> hoge.fuga
=> nil

irb(main):050:0> hoge.bar
=> 123
irb(main):051:0> hoge.fuga
=> nil

irb(main):052:0> hoge.foo
=> 123
irb(main):053:0> hoge.fuga
=> 123
```
