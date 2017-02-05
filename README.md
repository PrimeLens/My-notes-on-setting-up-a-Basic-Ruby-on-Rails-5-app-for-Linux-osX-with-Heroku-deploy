# My notes on setting up a Basic Ruby on Rails 5 app for Linux/OSX with Heroku deploy

If you are wanting to download this repo and run it,  scroll down to relevant section

## Installation of the Rails 5 Boilerplate from scratch
#### Check that hombrew is installed

    $ brew help
    $ brew update

#### Use brew to install rbenv to manage different versions of Ruby
To install rbenv version manager 

    $ brew install rbenv

In the home folder you will need to edit file named .bash_profile `$ open ~/.bash_profile`<br/>
And add the following lines

    #PATH for rbenv
    export PATH="$HOME/.rbenv/shims:$PATH"

note: if u ever need rbenv to recalc what available versions are there type `rbenv rehash`

#### Switch to latest version of Ruby

    $ ruby -v                 # to get version. most likely its currently 2.0.0
    $ rbenv install -l        # to get a list of available versions
    $ rbenv install 2.4.0     # download latest version of ruby
    $ rbenv global 2.4.0      # set it so we use the new ver
                              # quit terminal and start it again
    $ ruby -v                 # => ruby 2.4.0p0

#### Install Rails

Install rails with the next command. Dont use sudo here as we dont want rails installed in root

    $ gem install rails -v 5.0.1
    $ rbenv rehash
    
Check the version of rails installed
    
    $ rails -v

#### Create a rails app 

    $ rails new myapp
    
If 'mkdir' permission problems do this inside app folder

    $ mkdir tmp
    $ sudo chmod 777 tmp

If 'log' permission problems do this inside app folder

    $ sudo chmod 777 log
    $ touch log/development.log

If 'unable to open database file' permission problems do this inside app folder

    $ sudo chmod 777 db

Blanket fix for app permission problems
    
    $ sudo chmod -R 777 myapp

Edit the Gemfile and add

    # set ruby version
    ruby '2.4.0'


#### Add github's .gitignore 

Get it from [https://github.com/github/gitignore/blob/master/Rails.gitignore](https://github.com/github/gitignore/blob/master/Rails.gitignore)

#### Start the local server at port 3000

    $ rails server

<br/>
## Downloading this Repo instead and running it
Clone the repo or download the zip and unpack it.<br/>
Change directory into the folder for this repo and install the bundler gem

    $ gem install bundler

Use bundler to download the gems and start the server

    $ bundle --without production
    $ rails server

Use browser to visit `http://localhost:3000/#`

Sometimes you might run into permission problems because Rails creates directories and files. If this happens I have found the following command helpful.  First change directory up a folder level, run the command then cd back into the folder before staring the server.

    $ sudo chmod -R 777 My-notes-on-setting-up-a-Basic-Ruby-on-Rails-5-app-for-Linux-osX-with-Heroku-deploy
    
<br/>
## Heroku deploy
#### Edit the Gemfile for Heroku deploy
Edit `Gemfile` in the root folder and delete 

    # Use sqlite3 as the database for Active Record
    gem 'sqlite3'

At the bottom of the file set up groups by adding the following

    group :development do
      gem'sqlite3'
    end

    group :production do
      gem 'pg'                # pg gem for postgres
      gem 'rails_12factor'    # heroku needs this
    end

Run the package manager to implement these changes

    $ bundle --without production

#### Local git to heroku deploy

    $ git init
    $ git add -A; git add.
    $ git commit -m "adding files for a test push to heroku"
    $ heroku login      # make sure heroku toolbelt is installed first
    $ heroku create
    $ git remote        # check the heroku remote is added
    $ git push heroku master

#### Github to Heroku CI Integration

- create a branch called staging (or whatever you want to call it)
- in Heroku create an app using the dashboard
- then in that app go to Deploy > Deploy method > Github
- hit Connect to GitHub and authorize the application, choose the staging branch
- hit Enable Automatic Deploys
- check box "Wait for CI to pass before deploy" if you want to have tests pass before deploy (see heroku and github docs)


#### Run the db migrate command 
You must do this once the first time you deploy to create the table in the db. I did this by adding the remote locally and running it locally 

    $ heroku run rake db:migrate

Now when you go to see the app on Heroku it will say _The page you were looking for doesn't exist._ and this is normal the Rails app is running!!<br/>
For an explanation why you see that message see here [http://stackoverflow.com/questions/17964830/where-is-the-default-welcome-aboard-page-located-in-my-app](http://stackoverflow.com/questions/17964830/where-is-the-default-welcome-aboard-page-located-in-my-app)<br/>
To correct this you must generate a controller (see below) 

<br/>
## Generate a controller

Lets say our home page is going to be about Films

    rails generate controller Films index

Then edit `/config/routes.rb` and delete `get 'films/index'` swapping it for

    # use the films controller
    resources :films  
    root 'films#index'

Then edit `/app/views/films/index.html.erb` and paste in 

    <h1>Hello World</h1>

If you commit the change and push it back up to Heroku, don't forget to run `heroku run rake db:migrate` again.


