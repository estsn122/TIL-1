## - `link_to`を押した時に、確認ダイアログを表示させる。
- 基本の形  
`<%= link_to 'リンク名', ○○_path, data: { confirm: 'ダイアログで表示させるメッセージ' } %>`

- 例文  
` data: { confirm: '本当に削除しますか？' }`

- Data-Confirm Modal  
https://github.com/ifad/data-confirm-modal

## - `board_path(:id)`を`board_path(board)`と書く。
- `board`から、オブジェクトを一意に表す値、つまり、`board.id`をRailsが推察してくれる。  
- board_path というメソッドがStringのURL情報を返り値としていて、その引数にはIDの数字だけでなくオブジェクト自体を渡してもIDからURLを生成できる」というイメージ。

```
# 同じURLを生成する
<%= link_to board_path("#{board.id}"), ・・・ do %>
<%= link_to board_path(board), ・・・ do %>
```
- 元のソース  
https://github.com/rails/rails/blob/master/actionview/lib/action_view/helpers/url_helper.rb
