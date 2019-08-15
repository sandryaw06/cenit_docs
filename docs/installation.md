# Deploy your own server

## With the Heroku Button

[![Deploy to Heroku](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)

## Without It

* Clone the repo and change directory to it
* Log in with the Heroku Toolbelt and create an app: `heroku create`
* Use the mLab addon: `heroku addons:create mongolab:sandbox`
* Use the rabbitmq addon: `heroku addons:create "cloudamqp:lemur"`
* Deploy it with: `git push heroku master`
* Open in the browser: `heroku open`

##  With Docker compose.

After [install docker compose](https://docs.docker.com/compose/install)

Then run docker compose:

```batch
docker-compose build
docker-compose up
```

and visit the browser: `localhost:3000`

# Frameworks and Tools

* Ruby on Rails
* MongoDB
* Mongoid and Mongoff as Object Document Mapper (ODM)
* rails_admin, for build admin panel.
* RabbitMQ for internal pipeline messages.


# Create your own Cenit IO local server.

Clone the [GitHub cenit-io/cenit repo](https://github.com/cenit-io/cenit) and move to the **cenit** folder.

```
$ git clone https://github.com/cenit-io/cenit.git
$ cd cenit
```

Move to the development branch.

```
$ git checkout -b develop origin/develop
```

If you have previously cloned it ensure that you are in the **develop** branch...

```
$ git branch
> *develop
```

...and that it is updated.

```
$ git pull origin develop
> Already up-to-date.
```

Run the `bundle install` command to set up the required gems on your computer:

```
$ bundle install
```

Since Cenit IO uses Mongodb you don't need run any migrations, simply start the hub on port 3000, or any other of your
own choosing, just be mindful of that.

```
$ rails s -p 3000
```

If you have some trouble with *secret_key_base* running `rails s`, you can generate a random secret key value:

```
$ rake secret
```

Then copy this value and paste it in config/initializers/secret_token.rb:

```
Cenit::Application.config.secret_key_base = 'bla' # replace this
```

Browse *http://localhost:3000*. If you have any trouble please check that *mongodb* server is running.
You should also have a working installation of [RabbitMQ](http://www.rabbitmq.com), see below the guide to install
RabbitMQ.

If RabbitMQ is correctly installed when you run the rails server you should see:

```
 [*] Waiting for messages. To exit press CTRL+C
```

It uses Figaro gem to manage app configuration using ENV. Any of this variable is required to run a local server but maybe you consider some of them to run in the production environment

Then add to `config/application.yml` app configuration as reference review `config/application.example.yml`  

# Dependencies

Before generating your application, you will need:

* The Ruby language
* The Rails gem
* A working installation of [MongoDB](http://www.mongodb.org)
* A working installation of [RabbitMQ](http://www.rabbitmq.com)


## Installing MongoDB

If you don't have MongoDB installed on your computer, you'll need to install it and set it up to be always running on
your computer (run at launch).

On Mac OS X, the easiest way to install MongoDB is to install [Homebrew](http://brew.sh) and then run:

```
brew install mongodb
```

Homebrew will provide post-installation instructions to get MongoDB running. The last line of the installation output
shows you the MongoDB install location (for example, */usr/local/Cellar/mongodb/1.8.0-x86_64*). You'll find the MongoDB
configuration file there. After an installation using Homebrew, the default data directory will be
*/usr/local/var/mongodb*.

On the Debian GNU/Linux operating system, as of March 2013, the latest stable version is MongoDB 2.0.0. With MongoDB
2.0.0, the Mongoid gem must be version 3.0.x. See the
[Mongoid installation instructions](http://mongoid.org/en/mongoid/docs/installation.html#installation). Change your
`Gemfile` to use an earlier Mongoid version:

```
gem 'mongoid', github: 'mongoid/mongoid'
gem 'bson_ext', '~> 1.8.6'
```

## Installing RabbitMQ

The [RabbitMQ](http://www.rabbitmq.com) website has a good [installation guide](http://www.rabbitmq.com/download.html)
that addresses many operating systems. On Mac OS X, the fastest way to install RabbitMQ is with
[Homebrew](http://brew.sh):

```
brew install rabbitmq
```

then run it:

```
rabbitmq-server
```

On Debian and Ubuntu, you can either [download the RabbitMQ .deb package](http://www.rabbitmq.com/download.html) and
install it with [dpkg](http://www.debian.org/doc/manuals/debian-faq/ch-pkgtools.en.html) or make use of the
[apt repository](http://www.rabbitmq.com/install-debian.html) that the RabbitMQ team provides.

For RPM-based distributions like RedHat or CentOS, the RabbitMQ team provides an
[RPM package](http://www.rabbitmq.com/download.html).

```
Note: The RabbitMQ packages that ship with Ubuntu versions earlier than 11.10 are outdated and will not work with
Bunny 0.9.0 and later versions (you will need at least RabbitMQ v2.0 to use with this guide).
```

# How to install on Ubuntu server 16.04

## Update OS

```
sudo apt update
sudo apt dist-upgrade
sudo apt autoremove
reboot
```

## Install additional required packages

```
sudo apt install mongodb rabbitmq-server zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev nodejs git imagemagick libmagickwand-dev
```

## Install Ruby 2.5.5 with rbenv  (alternative RVM)

```
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL
rbenv install 2.5.5
rbenv global 2.5.5
exec $SHELL
```

## Confirm Ruby version

```
ruby -v
gem install bundler
exec $SHELL
```

## Install Cenit

```
git clone https://github.com/cenit-io/cenit.git
cd cenit
git checkout -b develop origin/develop
```

Confirm repository branch `> *develop`

```
git branch
```

Update development branch

```
git pull origin develop
```

## Install Cenit requirements

```
bundle install
```

## Create Cenit admin user and run Cenit

```
exec $SHELL
rake admin:create
rails s -p 3000 -b # The IP address of Ubuntu machine
```

## Importing first cross collection on Cenit local:

Once you have Cenit running in local you can start importing collections.

First, you need to create one translator that let you import all collections and data. So, go to Transformations/Parser and select New.

Write a namespace and a name for it. In style select Ruby.

In code write this:

```Ruby
if (parsed_data = JSON.parse(data)).is_a?(Array)
  parsed_data.each { |item| target_data_type.create_from_json!(item) }
else   
  target_data_type.create_from_json!(parsed_data)
end
```

Then save it.

Now you can import collections using the translator you have already created.

Example: Importing Basic collections

1. Export Basic collection. In Cenit.io search Basic cross collection and select the Export option. In translator select JSON Portable Exporter [Shared].

2. Import Basic collection. In your local Cenit, go to Collections/Shared Collections/All and select Import option. There select the translator you have just created and import the collection. You can see it on Collections/Shared Collections/All.