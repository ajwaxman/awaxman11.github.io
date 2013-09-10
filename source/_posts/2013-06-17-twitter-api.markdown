---
layout: post
title: "Using the Twitter API to Find People"
date: 2013-06-17 00:12
comments: true
categories: twitter ruby
---

#### Inspiration

Over the last year I've become a huge Twitter fan.  So much so that I had to move it off the front page of my phone so I wouldn't be (as) distracted throughout the day.  In addition to my personal account, I have several accounts for projects I'm working on.  I use these accounts to discover and interact with people that may be interested in these apps.

To discover people that I think may be interested, I look at recent followers of similar apps.  While this process worked alright, it was a very manual.  Also, one of the apps I'm working on is only available in NYC, and there was no way to search for people only living in NYC and the surrounding area.


#### Using the Twitter API

I decided that I could use the Twitter API to help automate this process.  For example, let's pretend that I am creating starting a Ruby Motion meetup in NYC.  I'd like to find people in the NYC area that are interested in Ruby Motion.  Using Twitter, I can find these people a couple of different ways:

<ol style="margin-left: 30px;">
  <li>Look for people that follow @RubyMotion</li>
  <li>Look for people that recently tweeted "#rubymotion"</li>
</ol>

In addition to finding people that match either of the criteria above, I also want to make sure they live in the NYC area.  

#### Getting Started

I first looked into the best way to access the Twitter API from a ruby environment.  As Twitter is a very popular app, I figured there was likely a good gem.  After searching for 'Twitter' on <a href="https://ruby-toolbox.com">www.ruby-toolbox.com</a>, I ended up deciding to go with the 'Twitter' gem.  After installing this gem in the terminal, I required the rubygems gem and twitter gem in my ruby file.  

``` ruby
require 'rubygems'
require 'twitter'
```

Next I read the gem documentation on <a href="https://github.com/sferik/twitter">github</a>, and followed the steps to register and authorize my app.  After registering my app on the <a href="https://dev.twitter.com/apps/new">Twitter Developer site</a>, it was just a few lines of code before I was up and running.

``` ruby
Twitter.configure do |config|
  config.consumer_key = "**********************"
  config.consumer_secret = "*****************************************"
  config.oauth_token = "**************************************************"
  config.oauth_token_secret = "*****************************************"
end
```

Now I was up and running!  To tweet about the new meetup was just a simple line away: 

``` ruby
Twitter.update("Come on out to our Ruby Motion meetup next Tuesday @ 6:30pm!")
```

Now for finding relevant people:

``` ruby
nyc_accounts = {}
index = 0
cursor = -1

# Loop through pages 1-10
while index <15 do
    
    # Iterate through followes
    followers = Twitter.followers("RubyMotion", :cursor => -1)

    # Save the users 
    followers.users.each do |user|
      rubymotion_accounts[user.screen_name] = user.location if user.location.downcase =~ /(nyc|york)/i
    end

    cursor = followers.next_cursor
    index+=1
end
```

The code above makes a call to the Twitter API to grab the followers for a sprcific Twitter account, in this case RubyMotion.  The way the Twitter API works, each call returns an object that contains an array of 20 seperate hashes, which contain information about each of the followers. Because the API rate limits of the Twitter API are relatively small, I decided to loop through only 15 pages of followers.

Next, I went through each Twitter object and added the username and location of the follower if the location contained either nyc or york (using regular expressions). The result is a hash where the keys are usernames and the values are locations for, containing information of people that follow RubyMotion and have a location that contains either 'NYC' or 'York'.

On to the next one:

``` ruby
rubymotion_accounts_2 = {}

Twitter.search("#rubymotion", :count => 100, :result_type => "recent").results.each do |status|
  rubymotion_accounts_2[status.attrs[:user][:screen_name]]=status.attrs[:user][:location] if status.attrs[:user][:location].downcase =~ /(nyc|york)/i
end

``` 

For the second call, I a utilizing the search method, and querying the most recent 100 tweets that contain the #rubymotion tag.  This call returns a Twitter object that contains an array of all the tweets.  Each item of the array contains a hash with information about the user.  I then saved the username and location of the tweeter if the location of that person contained 'NYC' or 'York' again.  

Saving the data into a database:

``` ruby
require "sqlite3"
require 'open-uri'

db = SQLite3::Database.new "twitter-bot.db"

db.execute <<-SQL
 
  create table twitter_handles (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    twitter_handle TEXT,
    city TEXT
  );
SQL

rubymotion_accounts.each do |handle, city|
  db.execute("INSERT INTO twitter_handles (twitter_handle, city) 
              VALUES (?, ?)", [handle, city])
end

rubymotion_accounts_2.each do |handle, city|
  db.execute("INSERT INTO twitter_handles (twitter_handle, city) 
              VALUES (?, ?)", [handle, city])
end
```
Lastly, I wrote a schema.rb file to create a table and iterate through the 2 hashes I created above and insert the information into the new data.


### Areas of Future Exploration

One problem I kept on running into was exceeding my API rate limit.  For example, in the first API call, the reason I only scroll through 15 pages is because the Twitter API only allows me to make 15 calls within a 15 minute window.  While finding 300 relevant people seems like a lot, when you add the location constraint it ends up not being that many people. Similarly in the 2nd API call, I only grab the last 100 tweets because that is all the API lets me grab at a time. This made it really hard to find many people that were relevant for this specific search.  When I ran this search I actually ended up with no results because, presumably because of the 100 tweets grabbed, no users were located in New York. 