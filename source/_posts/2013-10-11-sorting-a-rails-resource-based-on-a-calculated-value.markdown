---
layout: post
title: "Sorting a Rails Resource Based on a Calculated Value"
date: 2013-10-11 09:32
comments: true
categories: rails sorting
---
### The Setup

I recently went to a mobile payments hackathon and worked on a [reminder app](http://lit-sands-3416.herokuapp.com/) with my co-founder of [Flock](http://flockwithme.com). You input the name of your item and the day of the month that it's due. The app then shows you how long you have to complete the given task based on a green to red spectrum inspired by Clear.

{% img /images/reminder_app.png %}

Being a hackathon project, we wanted to keep the app simple. It contained a Reminder model with 2 attributes: 1) name and 2) day of the month you want to complete the task.

### The Problem

I got to a point where I wanted sort the items by number of days left until the item was due. 

If I had a column in the database called 'days_until_due' I could have used the order method:

```ruby
Reminder.order('days_until_due ASC')
```

But I didn't have a database column called 'days_until_due'…

This got me asking myself, "What's the best way to sort a model based on a calculated value instead of a value stored in the database?"

### The Solution

After googling around I stumbled upon a nice solution. It suggested creating an instance method to calculate the value, and then creating a class method that combines a query with the sort_by method.

##### Step 1: Create an instance method to calculate the value

In reminder.rb, I created an instance method to find out how many days until the item is due based on the day of the month the item was suppoed to be completed by and the current date:

```ruby
def days_until_due
  today = Time.now
  simple_today = Time.new(today.year, today.month, today.day)
  if day >= today.day
    month_due = today.month
  else
    month_due = today.month + 1
  end
  day_due = Time.new(today.year,month_due,day)
  return ((day_due - simple_today)/(60*60*24)).to_i
end
```
##### Step 2: Create a class method to query and sort the model

Again in reminder.rb, I then created a class method, which combined a query, sort_by, and the instance method from above.

```ruby
def self.sorted_by_days_until_due
  Reminder.all.sort_by(&:days_until_due)
end
```
You may be asking yourself what is going on in that one line method? Let's break it down step by step. First, Reminder.all returns an array of all the reminders (unsorted).

```ruby
Reminder.all.class # => Array
```

Next, I call the sort_by method on this array. This method takes a block as an argument and generates a sorted array by mapping the values through the given block. If no block is given, an enumerator is returned instead. Below is an example: 

```ruby
array = ["Michael", "Adam", "Jen"]

array.sort_by{|word| word.length} # => ["Jen", "Adam", "Michael"] 

array.sort_by # => #<Enumerator: ["Michael", "Adam", "Jen"]:sort_by>
```

Now you're probably asking yourself, but how does '&:days_until_due' translate into a block? It has to do with procs and blocks. If you're unfamiliar with these terms, you should check out a [post](http://awaxman11.github.io/blog/2013/08/05/what-is-the-difference-between-a-block/) I wrote discussing these parts of ruby. In one sentence, the '&:' syntax is converting the instance method into a proc, and then converting the proc into a block, which is what Array#sort_by takes as an argument.

Below I will make the example above look like the method I used in my reminder app.

```ruby
array = ["Michael", "Adam", "Jen"]

# Passing in a block directly
array.sort_by{|word| word.length} # => ["Jen", "Adam", "Michael"] 

# Creating a proc and converting the block to a proc using the '&' syntax
proc = :length.to_proc # => #<Proc:0x007f98a225f700> 
array.sort_by(&proc) # => ["Jen", "Adam", "Michael"]

# Creating a proc and converting the block to a proc in one step
array.sort_by(&:length) # => ["Jen", "Adam", "Michael"]

```
The above syntax works by combining implicit type casting with the '&' operator. The '&' operator is used in an argument list to convert a Proc instance into a block. If you combine the operator with something other than a Proc instance, implicit type casting will try to convert it to a Proc instance using the to_proc method. Since Symbol#to_proc exists, when we pass a symbol after the '&' operator, it is converted into a proc, which is then converted into a block.

Going back to my original problem, all this talk about procs and blocks and type casting is what allowed me to create the following one line method:

```ruby
def self.sorted_by_days_until_due
  Reminder.all.sort_by(&:days_until_due)
end
```

This method allowed me to succinctly include a sorted array of reminders in my view with the following: 

```ruby
@reminders = Reminder.sorted_by_days_until_due
```

Now back to coding!












