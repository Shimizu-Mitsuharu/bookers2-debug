

### テスト手順の自動化
gemを入れて、specファイルを移動して、テストを実行するコマンドを打つという手順をまとめたcheck.shというファイルを作成した
アプリケーションのルートディレクトリにおいて
bash check.sh
というコマンドを打つと最後まで自動で終了する
ディレクトリが野原のディレクトリにあっているため、自分で修正が必要

### 実行コマンド
bundle exec rspec spec/ --format documentation

### 注意
カラム名が違うとほとんどのテストに失敗してしまうが、このコマンドですべてのファイルの文字列を変更することができる
例はopinionというカラム名で作られていたため、それをすべてbodyというカラム名に変更した
find . -type f | xargs sed -i 's/opinion/body/g'

一回テストを試していると、テスト用のデータベースtest.sqlite3ができているため、カラム名を変更したのちに再びやる時は
rm db/test.sqlite3によって、ファイルを削除してから実行する


①root.rb
・add "end"
・before
　resources :users,only: [:show,:index,:edit,:update]
　resources :books
　devise_for :users

  after
　devise_for :users
　resources :users,only: [:show,:index,:edit,:update]
　resources :books


②controller
2-1:application_controller.rb
・#9 root_path
     user_path(current_user)
2-2:books_controller.rb
・#2 add
  before_action :baria_user, only: [:edit, :update]
・#5 add
  @new_book = Book.new
  @user = @book.user
・#11 add
  @book = Book.new
・#15 add
  @book.user_id = current_user.id
・#39 fix delete →　destory
・#51 add
  def baria_user
	unless Book.find(params[:id]).user.id.to_i == current_user.id
		redirect_to books_path
	end
 end
・users_controller.rb
add "end"

③gem
・add bootstrap reference https://web-camp.io/magazine/archives/16848

④layouts/application.html.erb
・#42　add
<!--       <p class="alert"><%= alert %></p>
    </div>
    <main>
      <div class="container">
        <%= yield %>
      </div>
    </main> -->