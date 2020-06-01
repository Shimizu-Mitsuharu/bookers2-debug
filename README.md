

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


1：root.rb
・add "end"
・before
　resources :users,only: [:show,:index,:edit,:update]
　resources :books
　devise_for :users

  after
　devise_for :users
　resources :users,only: [:show,:index,:edit,:update]
　resources :books


２：controller
②-1:application_controller.rb
・#9 root_path
     user_path(current_user)

②-2:books_controller.rb
・#2 add
  before_action :baria_user, only: [:edit, :update]
・#5 add
  @new_book = Book.new
  @user = @book.user
・#11 add
  @book = Book.new
・#15 add
  @book.user_id = current_user.id
・#39 fix
  delete →　destory
・#51 add
  def baria_user
	unless Book.find(params[:id]).user.id.to_i == current_user.id
		redirect_to books_path
	end
 end

②-3:home_controller.rb
・#2 add
  before_action :authenticate_user!, except: [:top, :about]

②-4:users_controller.rb
・#2 add　:edit,
	before_action :baria_user, only: [:update]
	before_action :baria_user, only: [:edit, :update]
・#21 delete　users_path(・・・）
    redirect_to users_path(@user), notice: "successfully updated user!"
    redirect_to @user, notice: "successfully updated user!"
・#23 Fix
  	render "show"
  	render "edit"
 ・#38 add
    "end"


３：models
③−1：book.rb
・#2 Fix
	has_many :user
	belongs_to :user
③-2：user.rb
・#7 Fix
    belongs_to :books
    has_many :books, dependent: :destroy
・#12 add
    validates :introduction, length: {maximum: 50}

４：view
①books
①-1:_newform.html.erb
・#2 add
<!-- <div class="error">
	<% if book.errors.any? %>
	<%= pluralize(book.errors.count, "error") %><span>prohibited this book from being saved:</span>
	<ul>
		<% book.errors.full_messages.each do |message| %>
		<li><%= message %></li>
		<% end %> -->
・#19 Fix
	<%= f.label :body %> <!-- ここを変更 -->
	<%= f.label :opinion %> <!-- ここを変更 -->
①-2:show.html.erb
・#6 add
    <%= render 'users/profile' %>
    <%= render 'users/profile',user:@user %>
・#9 add
	<%= render 'books/newform' %>
	<%= render 'books/newform', book:@new_book %>
・#27 and #31 add
	<% if @book.user == current_user %>
	<% end %>

②home
②-1:top.html.erb
・#11 delete s
    <p><%=link_to "Log in", new_user_sessions_path, class: "btn btn-info col-xs-10 col-xs-offset-1" %></p>
    <p><%=link_to "Log in", new_user_session_path, class: "btn btn-info col-xs-10 col-xs-offset-1" %></p>

・#14 delete s
	<p><%=link_to "Sign up", new_user_registrations_path, class: "btn btn-success col-xs-10 col-xs-offset-1" %></p>
	<p><%=link_to "Sign up", new_user_registration_path, class: "btn btn-success col-xs-10 col-xs-offset-1" %></p>

③layouts
③-1:application.html.erb
・#42　add
<!--       <p class="alert"><%= alert %></p>
    </div>
    <main>
      <div class="container">
        <%= yield %>
      </div>
    </main> -->

④device
④-1:registrations/new.html.erb
    <%= f.name_field :name, autofocus: true, class: "name" %>
    <%= f.text_field :name, autofocus: true, class: "name" %>

⑤user
⑤-1: index.html.erb
・#1 add
<!-- <div class="row">
	<div class="col-xs-3"> -->
⑤-2: show.html.erb
・#1 add
<!-- <div class="row">
	<div class="col-xs-3"> -->
・#24 add @
  <% books.each do |book| %>
  <% @books.each do |book| %>


５：gem
・add bootstrap
　　reference https://web-camp.io/magazine/archives/16848