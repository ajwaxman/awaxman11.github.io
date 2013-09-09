---
layout: post
title: "What is the difference between a block, a proc, and a lambda in ruby?"
date: 2013-08-05 23:52
comments: true
categories: ruby
---

### What are blocks, procs, and lambdas? ###

<strong>Coder Talk</strong>: Examples of *closures* in ruby.

<strong>Plain old english</strong>: Ways of grouping code we want to run.

```ruby
# Block Examples

[1,2,3].each { |x| puts x*2 }   # block is in between the curly braces

[1,2,3].each do |x|
  puts x*2                    # block is everything between the do and end
end

# Proc Examples             
p = Proc.new { |x| puts x*2 }   
[1,2,3].each(&p)              # The '&' tells ruby to turn the proc into a block 

proc = Proc.new { puts "Hello World" }
proc.call                     # The body of the Proc object gets executed when called

# Lambda Examples            
lam = lambda { |x| puts x*2 }   
[1,2,3].each(&lam)  

lam = lambda { puts "Hello World" }
lam.call
```

While it looks like these are all very similar, there are subtle differences that I will cover below.

### Differences between Blocks and Procs ###

<ol style="margin-left: 23px;">
  <p></p>
  <li><strong>Procs are objects, blocks are not</strong></li>
  <p></p>


<p></p>A proc (notice the lowercase p) is an instance of the Proc class.

```ruby
p = Proc.new { puts "Hello World" }
```
This lets us call methods on it and assign it to variables. Procs can also return themselves.
```ruby
p.call  # prints 'Hello World'
p.class # returns 'Proc'
a = p   # a now equals p, a Proc instance
p       # returns a proc object '#<Proc:0x007f96b1a60eb0@(irb):46>'

```
<p></p>
In contrast, a block is just part of the *syntax* of a method call. It doesn't mean anything on a standalone basis and can only appear in argument lists.
```
{ puts "Hello World"}       # syntax error  
a = { puts "Hello World"}   # syntax error
[1,2,3].each {|x| puts x*2} # only works as part of the syntax of a method call
```
  <li><strong>At most one block can appear in an argument list</strong></li>

<p></p>In contrast, you can pass multiple procs to methods.  
```  ruby
def multiple_procs(proc1, proc2)
  proc1.call
  proc2.call
end

a = Proc.new { puts "First proc" }
b = Proc.new { puts "Second proc" }

multiple_procs(a,b) 
```
</ol>

### Differences between Procs and Lambdas ###

Before I get into the differences between procs and lambdas, it is important to mention that they are both Proc objects.
```ruby
proc = Proc.new { puts "Hello world" }
lam = lambda { puts "Hello World" }

proc.class # returns 'Proc'
lam.class  # returns 'Proc'
```
However, lambdas are a different 'flavor' of procs.  This slight difference is shown when returning the objects.
```ruby 
proc   # returns '#<Proc:0x007f96b1032d30@(irb):75>'
lam    # returns '<Proc:0x007f96b1b41938@(irb):76 (lambda)>'
```
The (lambda) notation is a reminder that while procs and lambdas are very similar, even both instances of the Proc class, they are also slightly different.  Below are the key differences.

<ol style="margin-left: 23px;">
  <li><strong>Lambdas check the number of arguments, while procs do not</strong></li>
  <p></p>

```ruby
lam = lambda { |x| puts x }    # creates a lambda that takes 1 argument
lam.call(2)                    # prints out 2
lam.call                       # ArgumentError: wrong number of arguments (0 for 1)
lam.call(1,2,3)                # ArgumentError: wrong number of arguments (3 for 1)
```

In contrast, procs don't care if they are passed the wrong number of arguments.  

```ruby
proc = Proc.new { |x| puts x } # creates a proc that takes 1 argument
proc.call(2)                   # prints out 2
proc.call                      # returns nil
proc.call(1,2,3)               # prints out 1 and forgets about the extra arguments
```
As shown above, procs don't freak out and raise errors if they are passed the wrong number of arguments. If the proc requires an argument but no argument is passed then the proc returns nil.  If too many arguments are passed than it ignores the extra arguments.

  <p></p>
  <li><strong>Lambdas and procs treat the 'return' keyword differently</strong></li>
  <p></p>

'return' inside of a lambda triggers the code right outside of the lambda code

```ruby
def lambda_test
  lam = lambda { return }
  lam.call
  puts "Hello world"
end

lambda_test                 # calling lambda_test prints 'Hello World'
```

'return' inside of a proc triggers the code outside of the method where the proc is being executed

```ruby
def proc_test
  proc = Proc.new { return }
  proc.call
  puts "Hello world"
end

proc_test                 # calling proc_test prints nothing
```
</ol>


### So what is a closure? ###

<strong>Coder Talk</strong>: 'A function or a reference to a function together with a referencing environment.  Unlike a plain function, closures allow a function to access non-local variables even when invoked outside of its immediate lexical scope.' - Wikipedia  

<strong>Plain old english</strong>: Similar to a suitcase, it's a group of code that when opened (i.e. called), contains whatever was in it when you packed it (i.e. created it).

```ruby
# Example of Proc objects preserving local context

def counter
  n = 0
  return Proc.new { n+= 1 }
end

a = counter                  
a.call            # returns 1
a.call            # returns 2

b = counter  
b.call            # returns 1

a.call            # returns 3
```

### Background Part 1: Lambda Calculus and Anonymous Functions ###

Lambda get its name from a type of calculus introduced in the 1930s to help investigate the foundations of mathematics.  Lambda calculus helps make computable functions easier to study by simplifying its semantics.  The most relevant of these simplifications is that is treats functions 'anonymously', meaning that no explicit names are given to functions.  

```ruby
sqsum(x,y) = x*x + y*y  #<-- normal function
(x,y) -> x*x + y*y #<-- anonymous function
```  

Generally speaking, in programming the term lambda refers to anonymous functions.  These anonymous functions are very common and explicit in some languages (i.e. Javascript) and implicit in others (i.e. Ruby).

### Background Part 2: Where Does the Name Proc Come From ###

Proc is short for procedure, which is a set of instructions packaged as a unit to perform a specific task.  In different languages these may be called functions, routines, methods, or the generic term callable units.  They are usually made to be called multiple times and from multiple times in a program.

### Summary Differences ###

<ol style="margin-left: 23px;">
  <li><strong>Procs are objects, blocks are not</strong></li>
  <li><strong>At most one block can appear in an argument list</strong></li>
  <li><strong>Lambdas check the number of arguments, while procs do not</strong></li>
  <li><strong>Lambdas and procs treat the 'return' keyword differently</strong></li>
</ol>







