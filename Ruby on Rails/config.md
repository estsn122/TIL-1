#### 参照
[config readme](https://github.com/rubyconfig/config)

[Railsガイド/Railsアプリケーションを設定する](https://railsguides.jp/configuring.html)

[Railsドキュメント/設定ファイル](https://railsdoc.com/config)

[Railsの設定情報](https://www.buildinsider.net/web/rubyonrails4/0205)

[プログラマーのための YAML 入門 (初級編)](https://magazine.rubyist.net/articles/0009/0009-YAML.html)

[Railsで定数を環境ごとに管理するrails_config（現 config）|Qiita|](https://qiita.com/yumiyon/items/32c6afb5e2e5b7ff369e)

# Railsアプリケーションの設定
- Railsアプリケーションの動作を調整する方法
- アプリケーション開始時に実行したいコードを追加する方法 

## 初期化コードの置き場所
- config/application.rb
- 環境に応じた設定ファイル
- イニシャライザ
- アフターイニシャライザ

## 設定ファイル
### 概要
`config/`以下の設定ファイルによって設定を変更
### 特徴
- config.パラメータ名 = 値」の形式で設定
- 設定を反映するには、サーバの再起動が必要
- 文字コードは、UTF-8

```
/config  
│ーーapplication.rb         …… すべての環境で共通の設定ファイル  
│ーー/environment	          …… 環境ごとの設定ファイル  
│　　　|ーーーdevelopment.rb	…… 開発環境での設定  
│　　　│ーーーtest.rb	        …… テスト環境での設定  
│　　　│ーーーproduction.rb	  …… 本番環境での設定  
│ーー/initializers	         …… その他の初期化処理＆設定情報  
│　　　│ーーーbacktrace_silencers.rb	   …… 例外バックトレースをフィルタ  
│　　　│ーーーfilter_parameter_logging.rb	    …… ロギングから除外するパラメータ情報の条件  
│　　　│ーーーinflections.rb	           …… 単数形／複数形の変換ルール  
│　　　│ーーーmime_types.rb	           …… アプリケーションで利用できるコンテンツタイプ（6.3.2項）  
│　　　│ーーーsecret_token.rb	           …… クッキーを署名するためのトークン情報（6.4.2項）  
│　　　│ーーーsession_store.rb	           …… セッション保存のための設定情報（6.4.3項）  
│ーー/locales                …… 国際化対応のためのリソースファイル（10.3節）  
```
- application.rbはアプリケーション共通の設定情報
- /environmentフォルダ配下の.rbファイルは各環境固有の設定情報
- /initializersフォルダ配下の.rbファイル（初期化ファイル）は、アプリケーション起動時にまとめてロードされる。

# DBの設定ファイル
## 
## 特徴
- YAML形式で記述
- 開発(development)、テスト(test)、本番(production)の3つの環境
