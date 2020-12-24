```ruby
def TrappingWater(arr)
  n = arr.length
  left = []
  right = []

  # 一番左をセット
  left[0] = arr[0]
  # 左から2番目をセット
  i = 1
  loop do
    if i >= n
      break
    else
      left[i] = [left[i-1], arr[i]].max
      i += 1
    end
  end

  # 右から2番目をセット
  right[n-1] = arr[n-1]
  # 右から3番目をセット
  i = n-2
  loop do
    if i < 0
      break
    else
      right[i] = [right[i+1], arr[i]].max
      i -= 1
    end
  end

  i = 0
  water = 0
  loop do
    if i >= n
      break
    else
      water += [left[i], right[i]].min - arr[i]
      i += 1
    end
  end

  return water
end


# keep this function call here 
puts TrappingWater(STDIN.gets)
```

<img width="225" alt="スクリーンショット 2020-12-24 22 00 46" src="https://user-images.githubusercontent.com/60560627/103090126-7c712b80-4633-11eb-9da9-fc80024fcdf8.png">
