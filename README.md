# My notes on setting up a Basic Ruby on Rails 5 app for Linux/OSX with Heroku deploy

## Installation of the Rails 5 Boilerplate
#### Check that hombrew is installed

    $ brew help
    $ brew update

#### Use brew to install rbenv to manage different versions of Ruby
To install version manager `$ brew install rbenv ruby-build`<br/>
In the home folder you will need to edit file named .bash_profile `$ open ~/.bash_profile`<br/>
And add the following lines

    #PATH for rbenv
    export PATH="$HOME/.rbenv/shims:$PATH"

note: if u ever need rbenv to recalc what available versions are there type `rbenv rehash`

#### Switch to latest version of Ruby

    $ ruby -v                 # to get version. most likely its currently 2.0.0
    $ rbenv install -l        # to get a list of available versions
    $ rbenv install 2.2.3     # download latest version of ruby
    $ rbenv global 2.2.3      # set it so we use the new ver
                              # quit terminal and start it again
    $ ruby -v                 # => ruby 2.2.3p173

#### Install Rails

    $ sudo gem install rails  # will install rails
    $ rails -v                # print the version installed

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


#### Add github's .gitignore 

Get it from [https://github.com/github/gitignore/blob/master/Rails.gitignore](https://github.com/github/gitignore/blob/master/Rails.gitignore)

#### Start the local server at port 3000

    $ rails server

<br/>
## Edit the Gemfile for Heroku deploy
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

Do the standard git stuff for a heroku push

    $ git init
    $ git add -A; git add.
    $ git commit -m "adding files for a test push to heroku"
    $ heroku login      # make sure heroku toolbelt is installed first
    $ heroku create
    $ git remote        # check the heroku remote is added
    $ git push heroku master

IMPORTANT you must also run the migrate command to create the table in the db

  $ heroku run rake db:migrate

