---
layout: post
title: "Deploying a Rails App With Capistrano (Part 1)"
date: 2013-09-09 12:03
comments: true
categories: ruby capistrano
---

I recently graduated from the Flatiron School, where I worked on a 6-week long project with a group of 3 other students.  Our project, [HireMe](https://github.com/awaxman11/HireMe), is a CRM platform to help manage the interview process. We built the app using Rails and deployed it onto a cloud server using Capistrano (along with Nginx and Passenger). For side projects I've used Heroku, so this was my first time deploying to my own server.

### What is Capistrano?

Capistrano is a remote server automation and deployment tool written in ruby. Put simply, it helps you get your code on a server and easily run commands on a server so your application is ready for the world to use.

### Using Capistrano

The first step in using capitstrano is installing the capistrano gem on your local machine. Capistrano only requires that your have access to your server via Secure Shell (SSH).

```bash
gem install capistrano
```

Once the gem is installed the next step is to cd into your repository's directory and run the following command:

```bash
capify .
```

This creates a Capfile, which is where Capistrano reads instructions from. This is what it looks like out of the package:

```ruby
load 'deploy'
# Uncomment if you are using Rails' asset pipeline
    # load 'deploy/assets'
load 'config/deploy' # remove this line to skip loading any of the default tasks
```

The capify command also creates a file in the config directory called deploy.rb, which  the Capfile loads from as you can see above. The deploy file contains information about the servers you want to connect to and the tasks you want to run on those servers.

Below is the default deploy.rb file: 

```ruby
set :application, "set your application name here"
set :repository,  "set your repository location here"

# set :scm, :git # You can set :scm explicitly or Capistrano will make an intelligent guess based on known version control directory names
# Or: `accurev`, `bzr`, `cvs`, `darcs`, `git`, `mercurial`, `perforce`, `subversion` or `none`

role :web, "your web-server here"                          # Your HTTP server, Apache/etc
role :app, "your app-server here"                          # This may be the same as your `Web` server
role :db,  "your primary db-server here", :primary => true # This is where Rails migrations will run
role :db,  "your slave db-server here"

# if you want to clean up old releases on each deploy uncomment this:
# after "deploy:restart", "deploy:cleanup"

# if you're still using the script/reaper helper you will need
# these http://github.com/rails/irs_process_scripts

# If you are using Passenger mod_rails uncomment this:
# namespace :deploy do
#   task :start do ; end
#   task :stop do ; end
#   task :restart, :roles => :app, :except => { :no_release => true } do
#     run "#{try_sudo} touch #{File.join(current_path,'tmp','restart.txt')}"
#   end
# end
```

We worked off of an awesome deployment guide by [Spike Grobstein](https://twitter.com/spike666) that recommended starting with the following deploy.rb file: 

```ruby
require 'bundler/capistrano' # for bundler support

set :application, "set your application name here"
set :repository,  "set your repository location here"

set :user, 'USERNAME'
set :deploy_to, "/home/#{ user }/#{ application }"
set :use_sudo, false

set :scm, :git

default_run_options[:pty] = true

role :web, "96.8.123.64"                          # Your HTTP server, Apache/etc
role :app, "96.8.123.64"                          # This may be the same as your `Web` server

# if you want to clean up old releases on each deploy uncomment this:
# after "deploy:restart", "deploy:cleanup"

# if you're still using the script/reaper helper you will need
# these http://github.com/rails/irs_process_scripts

# If you are using Passenger mod_rails uncomment this:
namespace :deploy do
 task :start do ; end
 task :stop do ; end
 task :restart, :roles => :app, :except => { :no_release => true } do
   run "#{try_sudo} touch #{File.join(current_path,'tmp','restart.txt')}"
 end
end
```

This version differs slightly from the default deploy.rb file, namely adding lines to set the user information, where to deploy to, and a couple other small settings. Below are some exaplanations of each of the lines in this file.

```ruby
set :application, "studentbody"
```
This code sets the application variable.  It will become the name of the overarching folder on the server and is also used in the deploy_to location a couple lines below.

```ruby
set :repository,  "set your repository location here"

#example
set :repository,  "git@github.com:YourGithubUsername/ExampleRepo.git"
```
The repository variable tells Capistrano where to find your code.  We used a github hosted repository.

```ruby
set :user, 'USERNAME'
```
The above line sets the name of the user we are deploying as.  For example if you are SSHing into your server with the following command 'ssh example@192.241.555.55' then you'd set :user to example.

```ruby
set :deploy_to, "/home/#{ user }/#{ application }"
```

This command sets the location of deployment.  In the case where the the user is jsmith11 and the app name is example-app, the above code would deploy the app to '/home/jsmith11/example-app' directory on the remote server.

```ruby
set :use_sudo, false
```
The user_sudo variable tells Capistrano whether or not to prefix sudo infront of all commands. Sudo is a prefix that allows you to run programs with the security priveledges of another user (commonly used with the superuser/root). In our case we were deploying to a location that our user owned, so we set this to false. 

```ruby
set :scm, :git
```

The scm variable sets the source-code-management system, which in our case was git.


In a follow up post I will talk more about the rest of the deploy.rb file, custom tasks we ran in our HireMe app, and getting the app up and running using the 'cap deploy' command.















 



