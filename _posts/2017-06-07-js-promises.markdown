---
layout: post
title:  "JS Promises"
date:   2017-07-08 09:34:24 +0200
categories: jekyll update
---

In the previous post, we used the XMLHttpRequest object which is a very simple and basic way of requesting data from the server. Recently, the ECMAScript 6 introduced a new API called Promises in the hopes of making asynchronous requests more pleasant. We’re gonna take a look at what Promises are and understand them through a little bit of an exercise.

### What are Promises?
I have to admit that when I first heard someone say, “It returns a Promise”, I immediately go blank. What do you mean by “Promise”?

*In all simplicity, Promises are just objects.* They serve as placeholders for the data you’re retrieving that has not yet returned. A Promise object immediately returns even before the HTTP Request has been resolved.

Promises have a `then` method. Therefore, a promise is considered `thenable`.

To understand the benefit of the `then` method, let’s take a step back and see how a typical HTTP request is made *without* a Promise.

Suppose that you want to retrieve 2 sets of data from the server — where the second dataset somewhat relies on the result of the first. You then write 2 async HTTP request you fire /another/ HTTP request from the callback function of the first request.

This method can easily go out of hand, which is then referred to as “ [callback hell](http://callbackhell.com/) “ where you invoke callbacks from callbacks from callbacks. Even though there are multiple ways of making it not look too complicated, using the Promise API is much more cleaner and easier to maintain and understand.

### How does a Promise work?
A Promise takes a function as a callback. That function takes two arguments:

- a function that is called when the request is resolved / successful (usually named "resolve")
- another function that is called when the request has failed (usually named "reject")

Note that those 2 functions are already prepared for you; you just need to use it accordingly.

Let’s create function that performs a simple asynchronous HTTP request. We wrap our HTTP request in a new Promise.

{% highlight javascript %}
function get(url) {
  return new Promise(function(resolve, reject) {
  		var xhr = new XMLHttpRequest();
		xhr.open('GET', url, true);
  		xhr.send();
  		xhr.addEventListener('readystatechange', function(e) {
      	if(xhr.status === 200) {
     		 	resolve(JSON.parse(xhr.responseText));
     		}
     		else {
       		reject(xhr.status);
     		}
		});
  });
}
{% endhighlight %}

We call the `resolve` and `reject` functions according to the result of the request.

… screenshot?

Above the `get` function, we assign a GitHub Gist (that returns a json data) to a variable called `pets`:

{% highlight javascript %}
var pets = 'https://gist.githubusercontent.com/JulianvBeek/99a950c6711215d41a88d184ab99d75c/raw/d8a4a71e4356e1f163d815b398a6b1984715db87/site.json';

function get(url) { ... }
{% endhighlight %}

We then call our `get` function, passing the `pets` variable as the argument:

{% highlight javascript %}
var pets = ‘…’;
function get(url) {…}

get(pets);
{% endhighlight %}

Now you may be wondering, what happens once the data has been successfully retrieved from the server? That’s when the `.then` method comes in.

When we call the `get` function for the first time, it returns a new Promise object, which means that we get access to the `.then` method.

The `.then` method takes a callback function. And inside that function, you can access the data that is returned. To catch any errors, we add the `.catch` method at the end of the chain.

{% highlight javascript %}
get(housePets)
  .then(
  function(data) {
    console.log(data);
    return get(zooPets);
  })
  .catch(function(error) {
    console.log('error!');
});
{% endhighlight %}

*Okay, that’s cool. But what if we need to call multiple HTTP requests?*
To perform multiple HTTP requests, we call our `get` function inside the `.then` method’s callback function. Remember how our `get` function returns a new Promise object? That means we’ll have access to another `.then` method, which we can use once the second dataset has been successfully retrieved.

{% highlight javascript %}
get(housePets)
  .then(
  function(data) {
    console.log(data);
    return get(zooPets); // call the get() function
  })
  .then(function(zooData) { // do something with the second 									data set once it has returned
    console.log(zooData);
  })
  .catch(function(error) {
    console.log('error!');
});
{% endhighlight %}

> You could compare the Promise API to the step by step of making a burger. First, you run off to get the bottom bun (initial request). When I have my bottom bun, I can THEN put in the lettuce, THEN the patty, THEN some cheese, THEN tomatoes, and so on. If, for some reason I am missing a bottom bun, I have to return to the customer and tell them that we can’t make burgers anymore (stating the error)

### Support
Note that Promises are not being supported across all browsers so you have to use polyfills!


### Conclusion
Promises serve as placeholders for the data you’re retrieving that has not yet returned. It gives us to have access to a `.then` method which allows us to access the data that has been returned. Promises makes asynchronous HTTP request easier to understand and maintain, avoiding what is known as “callback hell”.
