### - Gemのインストール

### - carrierwaveのuploaderを生成
```
rails g uploader image
```

以下のファイルが生成。ImageUploaderクラス。

```ruby
(app/uploaders/image_uploader.rb)
class ImageUploader < CarrierWave::Uploader::Base
  # Include RMagick or MiniMagick support:
  # include CarrierWave::RMagick
  # include CarrierWave::MiniMagick

  # Choose what kind of storage to use for this uploader:
  storage :file
  # storage :fog

  # Override the directory where uploaded files will be stored.
  # This is a sensible default for uploaders that are meant to be mounted:
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  # Provide a default URL as a default if there hasn't been a file uploaded:
  # 画像が設定されているかどうかの判定をview側で書かずに済む。
  def default_url(*_args)
    'board_placeholder.png'
  end

  # Process files as they are uploaded:
  # process scale: [200, 300]
  #
  # def scale(width, height)
  #   # do something
  # end

  # Create different versions of your uploaded files:
  # version :thumb do
  #   process resize_to_fit: [50, 50]
  # end

  # Add a white list of extensions which are allowed to be uploaded.
  # For images you might use something like this:
  def extension_whitelist
    %w[jpg jpeg gif png]
  end

  # Override the filename of the uploaded files:
  # Avoid using model.id or version_name here, see uploader/store.rb for details.
  # def filename
  #   "something.jpg" if original_filename
  # end
end

```

### - DBへ画像保存用のカラムを追加
```
rails g migration add_board_image_to_boards board_image:string
rails db:migrate
```

### - モデルへの紐付け
  - `mount_uploader :carrierwave用に作ったカラム名, carrierwaveの設定ファイルのクラス名`

```ruby
(app/models/board.rb)
class Board < ApplicationRecord
  belongs_to :user
  has_many :comments, dependent: :destroy
  has_many :bookmarks, dependent: :destroy

  mount_uploader :board_image, ImageUploader
  validates :title, presence: true, length: { maximum: 255 }
  validates :body, presence: true, length: { maximum: 65_535 }
end
```

### - コントローラー
```ruby
(app/controllers/boards_controller.rb)
def board_params
  params.require(:board).permit(:title, :body, :board_image, :board_image_cache)
end
```

### - 画像ファイルのアップロードとプレビュー
 - バリデーションエラーが発生しても画像ファイルをキャッシュとして記憶させる。
   - `hidden_field`に`画像保存用のカラム名_cache`を付与。

```erb
(app/views/boards/_form.html.erb)
<%= form_with model: board, local: true do |f| %>
     .
     .
     . 
  <div class="form_group, mb-2">
    <%= f.label :board_image %><br>
    <%= f.file_field :board_image, onchange: 'previewFileWithId(preview)', class: 'form-control mb-3', accept: 'image/*' %>
    <%= f.hidden_field :board_image_cache, class: 'form-control' %>
  </div>

  <div class="form_group, mb-2">
    <%= image_tag @board.board_image.url, id: 'preview' %>
  </div>
     .
     .
     .
<% end %>
```

```javascript
(app/assets/javascripts/preview.js)
function previewFileWithId(id) {
  const target = this.event.target;
  const file = target.files[0];
  const reader  = new FileReader();
  reader.onloadend = function () {
      preview.src = reader.result;
  }
  if (file) {
      reader.readAsDataURL(file);
  } else {
      preview.src = '';
  }
}
```

### - アップロードした画像の表示

``` ruby
(app/views/boards/_board.html.erb)
<%= image_tag board.board_image_url, class: 'card-img-top', size: '300x200' %>
```

### - プロフィール画像を全ページで表示させたい場合は、`sorcery`を使っているなら、`image_tag`で`current_user.カラム名_url`をURLに指定するだけ。

### - ローカル環境でアップロードした画像はアップロードしない様に.gitignoreで指定する
  - .gitignoreファイル(git initで生成されている)で、バージョン管理の対象外にする。
    - インターネット上に公開したく無い、リモートリポジトリ に反映したくないファイルやディレクトリを指定する。
  - すでにファイルをコミットしてからgitignoreを追記しても、ファイルが上がったままになるため、コミット後は`git rm --cached ファイル名`コマンドでgitの管理対象外に設定する。

```
(.gitignore)
/public/uploads
```
