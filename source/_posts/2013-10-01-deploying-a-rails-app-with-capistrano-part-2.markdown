---
layout: post
title: "Deploying a Rails App With Capistrano (Part 2)"
date: 2013-10-01 10:00
comments: true
categories: capistrano part2
---
My last [post](http://awaxman11.github.io/blog/2013/09/09/deploying-a-rails-app-with-capistrano/) outlined using Capistrano to deploy my 6-week long Flatiron School [project](http://demo.hire.flatironschool.com/). The post included a brief introduction of Capistrano, including what it is and the steps to get a Capfile (where Capistrano reads instructions from) into your app.  

In this post, I am going to continue to walk through a basic deploy.rb file.  If you don't know what what a deploy.rb file is you should check out the earlier [post](http://awaxman11.github.io/blog/2013/09/09/deploying-a-rails-app-with-capistrano/) first. 

```ruby
require 'bundler/capistrano' # for bundler support

set :application, "set your application name here"
set :repository,  "set your repository location here"

set :user, 'USERNAME'
set :deploy_to, "/home/#{ user }/#{ application }"
set :use_sudo, false

set :scm, :git

default_run_options[:pty] = true

role :web, "96.8.123.64"  # Your HTTP server, Apache/etc
role :app, "96.8.123.64"  # This may be the same as your `Web` server

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

In the previous post I left off with the set ':scm, :git line', so I will start with the following line:

```ruby
default_run_options[:pty] = true
```
This is included to make sure Capistrano interacts properly with the shell on our server. For example, when using github your server needs to be able to access your repo. This capistrano config settting is needed for the password prompt from git to work so you can access it.

``` ruby
role :web, "96.8.123.64" # Your HTTP server, Apache/etc
role :app, "96.8.123.64" # This may be the same as your Web server
```

The above two lines assign the web and app 'roles' to specific servers. Capistrano roles allow you to have different servers handle different parts of the app (server for app, server for handling requests, and server for the database). We used one server for all aspects so assigned all roles to the same server.

Since we used Passenger, we also uncommented the :deploy namespace as is instructed in the standard deploy.rb file. This part of the file is an example of Capistrano's task-oriented nature. Similar to rake tasks, we can create 'cap' tasks with the task keyword.  For example, take the following 3 lines of code:

```ruby
task :hello do
  puts "Hello World"
end
```

We can run this code with the following command in the terminal:

```bash
cap hello
```

We also used a common capistrano task to symlink specific files that were purposely excluded from our git repo. For example, we had an application.yml file with our Crunchbase API key and gmail username/password. Since we open sourced the repo, we had to make sure that file was included in our .gitignore. Because capistrano is getting the code from github, we have to manually link these ignored files to the server in order for them to be included.  The following task helps us do just that.
ruby
```
task :symlink_config, :roles => :app do 
  run "ln -nfs #{shared_path}/config/application.yml #{release_path}/config/application.yml"
end
```
In order for the symlink to work though, the file must already be in the shared folder on the server. You can securely copy a file from your local machine using a variation of the following command:

```bash
scp config/application.yml example@123.456.789.12:/home/example/example_app/shared
```

And that concludes my Capistrano configuration process.   To deploy and update the app I simply run the following command:

```
cap deploy
```

If you're interested in learning more about deploying a rails app with Capistrano, there is a good [railscast](http://railscasts.com/episodes/133-capistrano-tasks-revised) and also a nice [wiki](https://github.com/capistrano/capistrano/wiki) on Github.
