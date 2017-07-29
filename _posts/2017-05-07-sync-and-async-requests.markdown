---
layout: post
title:  "Synchronous and Asynchronous Requests in Javascript"
date:   2017-05-07 09:34:24 +0200
categories: tutorial
---

There are 2 types of requests that can Javascript can make: Synchronous and Asynchronous. But what is the difference between the two? And how do you make such requests?

The difference between synchronous and asynchronous requests can be best explained by [one of the comments from Stack Overflow](http://stackoverflow.com/questions/748175/asynchronous-vs-synchronous-execution-what-does-it-really-mean):

> SYNCHRONOUS
>
> You are in a queue to get a movie ticket. You cannot get one until everybody in front of you gets one, and the same applies to the people queued behind you.
>
> ASYNCHRONOUS
>
> You are in a restaurant with many other people. You order your food. Other people can also order their food, they don't have to wait for your food to be cooked and served to you before they can order. In the kitchen restaurant workers are continuously cooking, serving, and taking orders. People will get their food served as soon as it is cooked.
> Requesting data synchronously used to be the default way on how the browser works. When the script runs, the tasks are done one by one so if a request takes too long, the user is left hanging with a frozen browser. Another downside of this is every single request requires the entire page to be reloaded, adding to performance issues and bad user experience.

The introduction of asynchronous requests took the web by storm. Things work a little differently when you request data asynchronously. Asynchronous requests allows you to move on to another task before the previous one ever finishes. This functionality was given the term AJAX, or Asynchronous JavaScript and XML. So whenever you hear the term AJAX it basically means that they are handling data asynchronously.

## Make a HTTP Request

Now that we've stated the difference and when they are most useful, how exactly do we make a HTTP request? In this quick tutorial, we're gonna request a JSON file from a GitHub Gist synchronously (and asynchronously!) using the XMLHttpRequest method. Note that there are other, more modern ways to make a HTTP request using plain old Javascript but we're gonna go with the most basic one for now.

{% highlight javascript %}
//Instantiate a new XMLHttpRequest object
var xhr = new XMLHttpRequest();
{% endhighlight %}

If you log this variable in the console, you can see all the methods that are now available for us to use.

#### Initialise a request

The next step is to initialise a request using the `.open()` method. The `.open()` method takes 5 arguments, 3 of which are optional:

{% highlight javascript %}
// syntax: xhr.open('method', 'url', [async, user, password])
xhr.open('GET', 'https://gist.githubusercontent.com/JulianvBeek/99a950c6711215d41a88d184ab99d75c/raw/d8a4a71e4356e1f163d815b398a6b1984715db87/site.json');
{% endhighlight %}

Note that the third parameter, `async`, is a boolean that is default to `true`, which means that our request will be handled asynchronously (you should know what that means by now!). The `user` and `password` arguments are used for authentication and are `null` by default.

Once we've initialise a request, we then send it to the server:
{% highlight javascript %}
xhr.send();
{% endhighlight %}
Okay, our script now runs off to Serverland and fetches the data for us. Since we requested the data asynchronously, we can do other tasks instead of waiting for the response to return. But how do we know when the response has actually returned?

#### Listening for an event

Once we've sent a request to the server, we have to find out if the response has returned or if the request has failed. We do this by listening for a change in the `readyState()` method.

When we first initialise a new XMLHttpRequest object, the value of `readyState` is `0`. Then, when we call the `open()` method, the readyState value changes to `1`. When we call `send()`, the value changes to `2`. `3` stands for `Loading`. We want to listen for a state change of `4`, which means that the operation is complete.

What we can do is attach an event listener to our `xhr` variable, listening for the `readyStateChange` event, passing it a function that will be called when the state changes.

The server also gives back a status code, indicating if the response was successfully completed. There are several status codes that you can check for, but for now, we'll specifically check for a successful request that has the code of 200:

{% highlight javascript %}
xhr.addEventListener('readystatechange', function(e) {
  if(xhr.readyState === 4 && xhr.status === 200) {
    // do something with data
  } else {
    console.log('something went wrong.');
  }
});
{% endhighlight %}

#### Process the data

We made a request and confirmed that the status was successful. Now what?

What we can do is to read the `responseText` property returned by the `xhr​` object. This returns a JSON in the form of a big string. We can use `JSON.parse` to convert the string into a real JSON format.

{% highlight javascript %}
console.log(JSON.parse(xhr.responseText));
{% endhighlight %}

Now you have a raw data that you can play around with!

## Synchronous Requests

We can create synchronous requests using the XMLHttpRequest object by setting the third parameter to `false`. You might not notice the difference if you have a high speed internet but you can simulate a slow connection on the Network tab on your DevTools.

From the `No throttling` dropdown, you can add your custom network throttling profile or select one from the dropdown.

Next we add the third parameter to `xhr.open` and set it to false:

{% highlight javascript %}
xhr.open('GET', 'https://gist.githubusercontent.com/JulianvBeek/99a950c6711215d41a88d184ab99d75c/raw/d8a4a71e4356e1f163d815b398a6b1984715db87/site.json', false);
{% endhighlight %}

Add another `console.log` statement after the HTTP request. Run the code and see what happens.

See how the screen seems to have been frozen? With a slow connection combined with a synchronous request to the server, we didn't have a choice but to wait for the request to finish and come back before we can do something again.

## Conclusion

Synchronous requests waits and blocks until the response come back. In most cases, we want to use asynchronous requests to give our users a better experience.

Here's the full block of code:

{% highlight javascript %}
var xhr = new XMLHttpRequest();

xhr.open('GET', 'https://gist.githubusercontent.com/JulianvBeek/99a950c6711215d41a88d184ab99d75c/raw/d8a4a71e4356e1f163d815b398a6b1984715db87/site.json', false);
xhr.send();

xhr.addEventListener('readystatechange', function(e) {
 console.log(xhr.readyState);
 if(xhr.readyState === 4 && xhr.status === 200) {
   console.log(JSON.parse(xhr.responseText));
 }
 else if(xhr.readyState === 4 && xhr.status === 404){
   console.log('missing file');
 }
 else {
   console.log('something went wrong');
 }
});

console.log('hello');
{% endhighlight %}
