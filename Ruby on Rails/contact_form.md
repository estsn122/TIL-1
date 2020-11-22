```ruby
(config/routes.rb)
resources :contacts, only: %i[new] do
  collection do
    post :complete
  end
end
```

```ruby
(app/models/contact.rb)
class Contact
  include ActiveModel::Model
  attr_accessor :name, :email, :body

  validates :name, :email, :body, presence: true
end
```

```ruby
(app/controllers/contacts_controller.rb)
class ContactsController < ApplicationController
  def new
    @contact = Contact.new
  end

  def complete
    @contact = Contact.new(contact_params)
    if @contact.valid?
      ContactMailer.send_contact_form_for_sender(@contact).deliver_now
      ContactMailer.send_contact_form_for_admin(@contact).deliver_now
      flash[:success] = 'お問い合わせを受け付けました'
    else
      render :new
    end
  end

  private

  def contact_params
    params.require(:contact).permit(:name, :email, :body)
  end
end
```

```ruby
(config/locales/ja.yml)
ja:
  activemodel:
    models:
      contact: 'お問い合わせ'
    attributes:
      contact:
        name: 'お名前'
        email: 'メールアドレス'
        body: 'お問い合わせ内容'
```

```ruby
(app/mailers/contact_mailer.rb)
class ContactMailer < ApplicationMailer
  def send_contact_form_for_sender(contact)
    @contact = contact

    mail(to: @contact.email,
         subject: 'お問い合わせを受け付けました')
  end

  def send_contact_form_for_admin(contact)
    @contact = contact

    mail(to: 'admin@example.com',
         subject: 'お問い合わせがありました')
  end
end
```

```ruby
(app/views/contact_mailer/send_contact_form_for_admin.html.erb)
<p>お問い合わせがありました</p>

<h2>お名前: <%= @contact.name %></h2>
<h2>メールアドレス: <%= @contact.email %></h2>
<h2>お問い合わせ内容: <%= @contact.body %></h2>
```

```ruby
(app/views/contact_mailer/send_contact_form_for_sender.html.erb)
<p>以下の内容でお問い合わせを受け付けました。</p>

<h2>お名前: <%= @contact.name %></h2>
<h2>メールアドレス: <%= @contact.email %></h2>
<h2>お問い合わせ内容: <%= @contact.body %></h2>
```

- [[Rails 4.1] ActiveModelを使って、DBと関係ないFormを作り、確認画面付きのお問い合わせフォームを作る方法](https://qiita.com/colorrabbit/items/787f914497693cb38e6c)
- [シンプルにお問い合わせ機能を実装する](https://qiita.com/s_rkamot/items/d6d0e9abb43383342fd6)
- [Ralsガイド Action Mailer の基礎](https://railsguides.jp/action_mailer_basics.html)
- [お問い合わせフォームの作り方（Sendgrid・Actionmailer）](https://www.blograils.com/posts/rails-contactform)
