## ネスト


## shallowオプション

## 単数形リソース
- ユーザーがページを表示する際に、idを一切参照しないリソースが使われることがある。
  - たとえば、/profileでは常に「現在ログインしているユーザー自身」のプロファイルを表示し、他のユーザーidを参照する必要がない時

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
