---
layout: post
title: "How to deploy an app in 5 minutes using roots"
date: 2013-08-20 00:03
comments: true
categories: javascript
---
### The Ingredients ###

<ul style="margin-left: 30px;">
  <li><strong><a href="http://nodejs.org/">node.js</a></strong>: server-side JavaScript</li>
  <li><strong><a href="https://heroku.com">heroku</a></strong>: cloud platform as a service to help you easily deploy your apps</li>
  <li><strong><a href="http://roots.cx">roots</a></strong> (the special sauce): a tool for quickly building beautiful and efficient web products</li>
</ul>


### The Steps ###

#### 1. Install node ####

If you don't have node.js, install it from <a href="http://nodejs.org/">nodejs.org</a>.

#### 2. Install roots ####

Once node.js is installed, you can can install roots in the terminal with the following command:

```bash
npm install roots -g # prefix with sudo if you get a permission error
```

#### 3. Create a new roots project ####

You can create a new roots project with the following command in the terminal:

```bash
roots new [name of project] # i.e. roots new new_project
```

#### 4. Start the local server ####

Once you've switched into the new project folder, you can start the app on localhost:1111 with the following command: 

```bash
roots watch
```

#### 5. Deploy to heroku ####

Now you're one small command away from having your app up on Heroku for the world to see:
```bash
roots deploy
```

Yes, it's that easy.  If you don't believe me checkout the <a href="http://ancient-escarpment-6672.herokuapp.com/">app</a> I made while going through the roots tutorial.

Checkout your own app with the following command:
```bash
heroku open
``` 

Note: The standard homepage markup is placed in a views folder with the name 'index.jade'.  If you don't want to use the default roots stack (jade, stylus, and coffeescript), you can revert to an html, css, and javascript stack by adding the --basic flag to your roots new command. 

### Awesome features ###

In addition to helping you get an app up and running in just a couple minutes, roots also has a ton of amazing built in features.

<strong><a href="http://livereload.com/">LiveReload</a></strong>: lets you see your markup and styling changes without having to refresh your browser
<strong><a href="http://roots.cx/axis/">Axis | Better CSS</a></strong>: a css library built on top of stylus that helps take the pain out of traditional css

### Axis in action ###

An example of how Axis makes css easier is how it simplifies border radius.  The following code shows how to create a border radius that works across different browsers: first using standard css, and then using Axis.

```css
/* Standard css border radius styling */
div {
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border-radius: 5px;
}

/* Using axis to add a border radius */
div
  border-radius: 5px; /* vendor prefixes are added automatically */
``` 

The button in the example <a href="http://ancient-escarpment-6672.herokuapp.com/">page</a> uses the following code: 

```css
.button a
  simple-button: blue 15
  transition()
```
Border-radius? Included. An easy on the eyes blue? Included. Darken on hover? Included. Yep, it's super convenient. There are a lot of other cool features you can check out via the library's <a href="http://roots.cx/axis/">documentation</a>.

### This is just the tip of the iceberg ###

There are a lot of other awesome features that I haven't talked about above - for example dynamic content and js templates.  The roots creator, <a href="https://twitter.com/jescalan">@jescalan</a>, has some great video tutorials that you can find <a href="http://roots.cx/#tutorials">here</a> if you are interested in learning more.














