---
layout: post
title: "Getting Into CSS3 Animations"
date: 2014-01-25 15:56
comments: true
categories: frontend css animation design
---

I recently had the opprtunity to dive into some CSS3 animations. I've used libraries like [animate.css](animate.css) and done animations with javascript, but never did any custom CSS3 work

#### The Task

We recently updated our 'tracking' iconography at SeatGeek to match our new iPhone app. The lead designer created the heart icon with different states in a PSD and also created the animation below:

![](https://f.cloud.github.com/assets/4431362/1728894/c3f1a952-62b6-11e3-819f-ed196c859568.gif)

#### What is CSS3 Animation?

In CSS, animation is an effect that lets an element gradually change styles. You create the animations with the @keyframes keyword, which is followed by the name of the animation.


```css
@keyframes heartAnimation {
  /* Animation code goes here */  
}
```

To make the animation cross browser compatible you need to use vendor prefixes:


```css
@keyframes heartAnimation {
  /* IE 10+ */
}

@-webkit-keyframes heartAnimation {
  /* Safari 4+ */
}

@-moz-keyframes heartAnimation {
  /* Fx 5+ */
}

@-o-keyframes heartAnimation {
  /* Opera 12+ */
}
``` 
However, for the rest of this post I will exclude the vendor prefixes for the sake of space.

The next step is to add the animation effects and decide when they happen. You can do this with either percentages from 0% to 100% or with the 'from' and 'to' keyword for simple animations with just a starting and ending state. Below is an example for changing the background color from yellow to blue, and then yellow to green to blue.

```css
@keyframes colorChange {
  from {background: yellow;}
  to   {background: blue;}
}

@keyframes colorChange {
  0%   {background: yellow;}
  50%  {background: green;}
  100% {background: blue;}
}

```

Once the keyframes are created, you can call the animations as CSS properties. For example the code below would run the colorChange animation above 2 times for a 2s duration:

```css
.color-animation {
  animation-name: changeColor;
  animation-iteration-count: 2;
  animation-duration: 2s;
}

/* Shorthand */
.color-animation {
  animation: changeColor 2 2s;
}

```
You can check out all the CSS3 animation properties [here](http://www.w3schools.com/cssref/css3_pr_animation.asp)

#### Planning Out the Animation

After watching the gif several times, I realized it was a slight contraction followed by an expansion to a size slightly larger than the original size, and then back to the original size.

#### Heart Clicked Animation

Using the CSS3 keyframes and animation syntax above, here is the code I used to make the animation in the gif at the top of this page. It uses the css transform and property to scale the image. 

```css
@keyframes heartAnimation {
  0%  {transform: scale(1,1)}
  20% {transform: scale(0.9,0.9)}
  50% {transform: scale(1.15,1.15)}
  80% {transform: scale(1,1)}
}

.toggle-animation {
  animation: heartAnimation 0.7s; // no iteration count is needed as the default is 1 time
}
```
For the image, I was using a sprite, so I also needed to change the position of the image to get the red background:

```css
.toggle-animation {
  background: url('../images/animation-example-sprite.png') no-repeat -320px 0;
  animation: heartAnimation 0.7s; // no iteration count is needed as the default is 1 times
}
```

#### Loading Animation

For a loading state, I made the heart white and pulsate in-and-out infinitely. It also scales down and back to the original size, instead of getting slightly larger than the original size before going to the original state like in the heartAnimation code above. Below is the code for the loading state:

```css
@keyframes loading {
  0% {transform: scale(1,1) }
  50% {transform: scale(0.8,0.8) }
  100% {transform: scale(1,1) }
}

/* Notice the added 'infinite' to is used to make the animation-iteration-count */

.toggle-loading {
  background: url('../images/animation-example-sprite.png') no-repeat -160px 0; // make background white
  animation: loading 1s infinite;
  -webkit-animation: loading 1s infinite;
  -moz-animation: loading 1s infinite;
  -o-animation: loading 1s infinite;
}
```

#### Check Out Demos of the Animations

Here is a site I built to demo the animations:

[http://heart-animation.herokuapp.com/](http://heart-animation.herokuapp.com/)

Below is the JS I used to make the animations happen when each icon is clicked. The JS adds and removes the classes that I added the animation properties to.

```js
$(document).ready(function(){
  
  $('.animation-1 .image').on('click', function(){
    $(this).toggleClass('toggle-animation');
  });

  $('.animation-2 .image').on('click', function(){
    $(this).toggleClass('toggle-animation-slow');
  });

  $('.animation-3 .image').on('click', function(){
    $(this).toggleClass('toggle-loading');
  });
  
});
```

    