---
layout: post
title: "Learning about AJAX"
date: 2013-07-21 23:13
comments: true
categories: 
---

As someone who loves UX and design, it is not surprising that I've been wanting to learn about JavaScript - the language of the browser that allows for cool client side interactions. I've used many JS libraries, but wanted to dive deeper into AJAX. Before today I didn't even know what it meant, just that it was something I wanted to know more about.

### So What is AJAX? ###

AJAX, or Asynchronous Javascript And XML, is a client side technique for communication with a web server.  In other words, it allows you to fetch data 'in the background' without having to reload a whole page.

In a typical web request, you send a URL request to the server and the server responds with the corresponding HTML, CSS, and JavaScript that generates a full new page in your browser.  

In contrast, in a typical AJAX request, the HTML, CSS, and JavaScript is already loaded. Instead of making a URL request for another whole page, you use JavaScript to talk to the server and receive smaller pieces of information that can range from HTML to other data formats like JSON and XML. The JavaScript then acts on the response and updates the page accordingly, without having to refresh the entire page.

### What does an AJAX request look like? ###

``` javascript

$.ajax(url, settings)

```


 At a high level an AJAX request consists of the URL you a making a request to, and then corresponding settings to handle the response.  Below are a few of the more popular callbacks that make up the settings: 
<ul style="margin-left: 30px;">
  <li><strong>success</strong>: what to do if the URL request is successful</li>
  <li><strong>error</strong>: what to do if the URL request is unsuccessful</li>
  <li><strong>timeout</strong>: how long to allow the URL request to run before an error message pops up</li>
  <li><strong>beforeSend</strong>: runs before the AJAX request, good place to put a spinner</li>
	<li><strong>complete</strong>: runs after both success and error, good place to stop a spinner</li>
</ul>

### Examples ###

``` javascript
/* Example 1 */
$.ajax('index.html', {
  success: function(response) {
    $('.hello-world').html(response);
  }
});

```
Example 1: makes a request to 'index.html' and then inserts the response into the item in the DOM that has a class called "hello-world"

``` javascript
/* Example 2 */
$.ajax('profile.html?userId=1', {
  success: function(response) {
    $('.show').html(response);
  }
});

/* Example 3 */
$.ajax('profile.html', {
  success: function(response) {
    $('.show').html(response);
  },
  data: {"userId": 1}
	}
});

/* Example 4 */
$.ajax('profile.html', {
  success: function(response) {
    $('.show').html(response);
  },
  data: {"userId": $(".show").data("userId")}
	}
});

/* Corresponding HTML to go with Example 4 */
<div class="show" data-userId="1"></div>


```
These three examples all make a request to the url 'profile.html?userId=1' and show the various ways to send parameters with a request.  In example 2 the data is put manually into the URL.  In example 3 the data is put into a JavaScript object.  Lastly, in example 4 the AJAX request pulls data from HTML, which when combined with ERB tags can make the request very dynamic.

``` javascript
/* Example 5 */
$.ajax('index.html', {
  success: function(response) {
    $('.hello-world').html(response);
  },
  timeout: 3000,
  beforeSend: function() {
  	$('.hello-world').addClass('is-loading');
	},
	complete: function() {
  	$('.hello-world').removeClass('.is-loading');
	}
});

```

Example 5 demonstrates some of the callback settings.  In this AJAX request, if the response is not successfully made it will stop the request after 3 seconds (3000 milliseconds).  It also demonstrates common code for adding a loading animation while the request is being made.  





