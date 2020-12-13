- [MDN - Array.prototype.reduce()](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
- [MDN - Array.prototype.slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
- [【JavaScript基礎】Array.prototype.reduce() をしっかり理解する＆サンプル集](https://kde.hateblo.jp/entry/2018/10/13/065738)
- []()

```javascript
function TrappingWater(arr) { 
  var strlen = arr.length;
  var HeightLeft = arr.slice()
  var HeightRight = arr.slice()
  
  // 左側から計算
  for(var i=1; i < strlen; i++) {
    if(HeightLeft[i] < HeightLeft[i - 1]) {
      HeightLeft[i] = HeightLeft[i - 1]
    }
  }
  // 右側から計算
  for(var i= strlen - 2; i >= 0; i--) {
    if(HeightRight[i] < HeightRight[i + 1]) {
      HeightRight[i] = HeightRight[i + 1]
    }
  }

  return arr.reduce((prev, current, index) =>
    prev + Math.min(HeightLeft[index], HeightRight[index]) - current
  )
}

console.log(TrappingWater(readline()));
```
