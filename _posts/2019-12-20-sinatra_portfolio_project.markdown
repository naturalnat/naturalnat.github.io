---
layout: post
title:      "Sinatra Portfolio Project"
date:       2019-12-20 14:26:07 -0500
permalink:  sinatra_portfolio_project
---

For my second portfolio project, I chose to  make a feel good app that allows users to feed cats a lil bit of salami as a treat. This was inspired by a popular game, Neko Atsume, and a meme. The layout of the site, too, is modeled after simple Xanga layouts from my childhood and similar low-quality .jpg images and plain text memes that I have seen online lately. 

![](https://imgur.com/a/fsHNYZK)

The app is run on a local server using shotgun. The MVC (models, views, controllers) framework relies on interaction between the three components to work. The models are the "logic" of the app, and they manipulate data.

`class User < ActiveRecord::Base
  has_secure_password
  has_many :cats
end
`
My user.rb model associates cats with user data using ActiveRecord database, and has a secure password. 

The controllers are the link between the models and views, allowing the user to interact with the views, relying on the controller to transmit data between the two. 

```
class ApplicationController < Sinatra::Base
  configure do
    set :public_folder, 'public'
    set :views, 'app/views'
    enable :sessions
    set :session_secret, 'alittlesalami'
    use Rack::Flash
  end

  get '/' do
    if Helpers.is_logged_in?(session)
      redirect to '/allcats'
    else
      erb :index
    end
  end
end
```

My application_controller.rb controller sets a public folder, enables sessions and sets an ID, which is a way to store data for use with unique ID. This also redirects to the "home page" - the index - if the user is not logged in. Otherwise, it redirects to a page where the user can see their cats and interact with them. 

The index.erb file is a "view" - the part which users interact with directly. My index.erb file looks like this: 

```
<center>
  <h1>
    giv cats a lil treat!</h1>
  <img src="https://tinyurl.com/t99yf5z" width="500" height="420">

  <br>
  <br>

  <a href='/login' class="buttons">Sign In</a>
  <a href='/signup' class="buttons">Sign Up</a>
```

This is linked to a layout file that has the layout for the entire website to avoid repetitive code.  


I have a helpers file to validate a sessions ID and use that data for the website, such as user identity. I also use flash messages to display messages such as errors about unfilled text fields. 

Helpers.rb looks like this: 
It finds sessions ID of current user and checks if they are logged in. 

```
class Helpers

  def self.current_user(session)
    User.find(session[:user_id])
  end


  def self.is_logged_in?(session)
    session.include?(:user_id)
  end
end
```

This is an example from my users_controller.rb.

```

post '/signup' do
    if params[:username] == '' || params[:password] == '' || params[:email] == ''
      flash[:error] = 'Username, email and password fields must be filled'
      redirect to '/signup'
    elsif User.find_by(email: params[:email])
      flash[:error] = 'E-mail already in use. Please choose another'
    elsif User.find_by(username: params[:username])
      flash[:error] = 'Username already in use. Please choose another'
      redirect to '/signup'

    else
      @user = User.create(firstname: params[:firstname], lastname: params[:lastname], username: params[:username], password: params[:password], email: params[:email])
      session[:user_id] = @user.id
      redirect to '/create'
    end
  end`
	
	
	ActiveRecord database is used to store information. This is my users migration: 
	
	`class CreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :firstname
      t.string :lastname
      t.string :username
      t.string :email
      t.string :password_digest
    end
  end
end

```



My favorite part of creating this project, however, was setting up a counter allowing for users to limit the amount of times a cat can get fed salami, because afterall, they are a treat ;) 

This part of my cat controller sets a counter for the amount of times a cat can be fed salami. I set it to 10 for testing. 

```
get '/thankyoubrother/:name' do
    @cat = Cat.find_by_name(params[:name])
    if @cat.counter < 10
      @cat.counter = @cat.counter + 1
      @cat.save
      @cat
    end
    erb :'cats/thankyou'
  end
```


This finds the cat in the database by its name, and counts the number of times the cat was fed. 



```

class CreateCats < ActiveRecord::Migration[5.2]
  def change
    create_table :cats do |t|
      t.string :name
      t.string :user_id
      t.integer :counter, default: 0
    end
  end
end

```

My migration file contains the counter code, and keeps track of this in the database. 

The views file is where the magic happens -  where the user feeds the salami. 

```
<center>
  <br> <h2><%=@cat.name %> has been fed <%= @cat.counter %> times.<br> remember...cats can only have a lil salami! as a treat! :~)</h2>
<br>
  <img src="/images/salamisad.jpg">
  <% if @cat.counter < 10 %>
  <h1>Thamk you bröther!
    <br>
    May I hav mor salämi?
  </h1>
  <% else %>
  <h1> I can only have 10 salamis. They're a treat :~)
    <br> I'm full.
  </h1>
  <% end %>

  <a href="/allcats" class="buttons">Feed another cat</a>
  <br>
  <a href="/logout" class="buttons">Logout</a>
</center>
```

Here, it shows the number of times a cat has been fed, and displays a message based on the counter. 
If a cat has had fewer than 10 treats, it will display a message asking for more treats. Otherwise, it will tell the user its full! The user also has options to feed another cat and be redirected to the cat main list, or to log out. The main list of cats lists all the cats, allows users to select them and interact with them. Users are able to delete a cat, edit their names, and feed them salamis. 


