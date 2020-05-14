## Rakeタスク

```rubvy:lib/tasks/status_update.rake
namespace :status_update do
  desc '記事が公開待ち状態で、公開日時が過去の場合、ステータスを「公開」にする'
  # environmentでDBに接続
  task article_status: :environment do
    @articles = Article.where(state: :publish_wait)
    @articles.each do |article|
      if article.published_at.past?
        article.state = :published
        article.save!
      end
    end
  end
end
```

## cronの実行対象であるRakeタスクのテスト

```ruby:spec/lib/tasks/status_update_spec.rb
require 'rails_helper'
require 'rake'

describe 'StatusUpdate' do
  # すべてのテストの前に1度だけ実行
  before(:all) do
    # Rake::Applicationクラスは何...？
    @rake = Rake::Application.new
    # Rakeは、Rake というコマンドラインツールを扱うライブラリ
    Rake.application = @rake
    # 指定のRakeファイルを読み込む
    Rake.application.rake_require 'tasks/status_update'
    # Rakeタスクを実行する環境？
    Rake::Task.define_task(:environment)
  end

  # eachで各テストの前に毎回実行
  before(:each) do
    # Rakeタスクを使ったテストを何回か実行する時、2回目以降もRakeタスクを実行してくれる?
    @rake[task_name].reenable
  end


  describe '記事ステータスを更新するTask' do
    # task_nameの定義必要？
    let!(:task_name) { 'status_update:article_status' }
    let!(:publish_wait) { create(:article, :publish_wait, published_at: "2019-12-05 12:00" ) }
    let!(:user) { create(:user, :admin) }
    context 'ステータスが「公開待ち」で、公開日時が過去になっている場合' do
      it 'ステータスを「公開」に更新すること' do
        # invokeでRakeタスクを呼び出す
        @rake[task_name].invoke
        expect(publish_wait.state).to eq "published"
      end
    end
  end

end
```

## 上記テストのデバッグ

```ruby
$ bundle exec rspec spec/lib/tasks/status_update_spec.rb

== Seed from /Users/funesakisuke/workspace/runteq/332_ryota1116_runteq_learning_advanced/db/fixtures/test/sites.rb
 - Site {:id=>1, :name=>"Blog", :subtitle=>"Very awesome!"}

StatusUpdate
  記事ステータスを更新するTask
    ステータスが「公開待ち」で、公開日時が過去になっている場合

From: /Users/funesakisuke/workspace/runteq/332_ryota1116_runteq_learning_advanced/lib/tasks/status_update.rake @ line 11 :

     6:     @articles.each do |article|
     7:       if article.published_at.past?
     8:         
     9:         binding.pry
    10:         
 => 11:         article.state = :published
    12:         article.save!
    13:       end
    14:     end
    15:   end
    16: end

[1] pry(main)> article.state
=> "publish_wait"
[2] pry(main)> next

From: /Users/funesakisuke/workspace/runteq/332_ryota1116_runteq_learning_advanced/lib/tasks/status_update.rake @ line 12 :

     7:       if article.published_at.past?
     8:         
     9:         binding.pry
    10:         
    11:         article.state = :published
 => 12:         article.save!
    13:       end
    14:     end
    15:   end
    16: end

[2] pry(main)> article.state
=> "published"
[3] pry(main)> next

From: /Users/funesakisuke/.rbenv/versions/2.6.5/lib/ruby/2.6.0/monitor.rb @ line 237 MonitorMixin#mon_synchronize:

    230: def mon_synchronize
    231:   # Prevent interrupt on handling interrupts; for example timeout errors
    232:   # it may break locking state.
    233:   Thread.handle_interrupt(EXCEPTION_NEVER){ mon_enter }
    234:   begin
    235:     yield
    236:   ensure
 => 237:     Thread.handle_interrupt(EXCEPTION_NEVER){ mon_exit }
    238:   end
    239: end

[3] pry(#<Monitor>)> next

From: /Users/funesakisuke/workspace/runteq/332_ryota1116_runteq_learning_advanced/spec/lib/tasks/status_update_spec.rb @ line 34 :

    29:     context 'ステータスが「公開待ち」で、公開日時が過去になっている場合' do
    30:       it 'ステータスを「公開」に更新すること' do
    31:         # invokeでRakeを呼び出す？
    32:         @rake[task_name].invoke
    33:         
 => 34:         binding.pry
    35:         
    36:         expect(publish_wait.reload.state).to eq "published"
    37:       end
    38:     end
    39:   end

[3] pry(#<RSpec::ExampleGroups::StatusUpdate::Task::Nested>)> publish_wait.state
=> "publish_wait"
[4] pry(#<RSpec::ExampleGroups::StatusUpdate::Task::Nested>)> @rake[task_name] 
=> <Rake::Task status_update:article_status => [environment]>
[5] pry(#<RSpec::ExampleGroups::StatusUpdate::Task::Nested>)> next

From: /Users/funesakisuke/workspace/runteq/332_ryota1116_runteq_learning_advanced/spec/lib/tasks/status_update_spec.rb @ line 36 :

    31:         # invokeでRakeを呼び出す？
    32:         @rake[task_name].invoke
    33:         
    34:         binding.pry
    35:         
 => 36:         expect(publish_wait.reload.state).to eq "published"
    37:       end
    38:     end
    39:   end
    40: 
    41: end

[5] pry(#<RSpec::ExampleGroups::StatusUpdate::Task::Nested>)> publish_wait.reload.state
=> "published"
[6] pry(#<RSpec::ExampleGroups::StatusUpdate::Task::Nested>)> publish_wait.state
=> "published"
```
