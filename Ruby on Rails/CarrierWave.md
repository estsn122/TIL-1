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

- DBテーブルへ画像保存用のカラムを追加
```
rails g migration add_board_image_to_boards board_image:string
rails db:migrate
```

- モデルの紐付け
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

- 画像ファイルのアップロードとプレビュー

```ruby
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

- アップロードした画像の表示

``` ruby
(app/views/boards/_board.html.erb)
<%= image_tag board.board_image_url, class: 'card-img-top', size: '300x200' %>
```
