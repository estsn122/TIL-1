

```ruby
def PalindromicSubstring(str)
  longest_str = ''
  first_i = 0
  while first_i < str.length
    next_i = 1
    while(first_i + next_i) <= str.length
      slice = str.slice(first_i, next_i)
      # 回文の中で最大文字列を入れる
      if (slice.length > longest_str.length) && (slice == slice.reverse)
        longest_str = slice
      end
      next_i += 1
    end
    first_i += 1
  end

  if longest_str.length > 2
    return longest_str
  else
    return 'none'
  end
end

# keep this function call here 
puts PalindromicSubstring(STDIN.gets)
```
