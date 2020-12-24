文字を削除して、回文を作ってくれる

```ruby
def PalindromeCreator(str)
  num = str.length
  first_i = 0
  second_i = 1
  if str == str.reverse && str.length > 2
    return str
  else
    while first_i <= num do
      while second_i <= num do
        slice_str = str.slice!(first_i, second_i)
        if str == str.reverse && str.length > 2
          return slice_str
        end
        second_i += 1
      end
      first_i += 1
    end
  end
  # code goes here
  # return result
  return 'not possible'
end
# keep this function call here 
puts PalindromeCreator(STDIN.gets)
```
