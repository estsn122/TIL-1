## ネスト


## shallowオプション

## 単数形リソース
- 単数リソースとは一つしか存在しないものを指す。表示させる時に使う。
  - ex.)アプリの設定など。アプリの設定はアプリ内に一つしか存在しない。
- ユーザーがページを表示する際に、idを一切参照しないリソースである。
 - /profileでは常に「現在ログインしているユーザー自身」のプロファイルを表示し、他のユーザーidを参照する必要がないとした場合、単数リソースを使えばよい。
 
```ruby
resource :profile
```

```terminal
new_profile   GET    /profile/new(.:format)         profiles#new
edit_profile  GET    /profile/edit(.:format)        profiles#edit
profile       GET    /profile(.:format)             profiles#show
              PATCH  /profile(.:format)             profiles#update
              PUT    /profile(.:format)             profiles#update
              DELETE /profile(.:format)             profiles#destroy
              POST   /profile(.:format)             profiles#create
```
