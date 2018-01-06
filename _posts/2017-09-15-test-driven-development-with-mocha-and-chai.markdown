---
layout: post
title:  "Test-Driven Development with Mocha and Chai"
date:   2017-09-15 09:00:00 +0200
categories: tutorial
---
So what exactly is Test-Driven Development?

Based on my understanding after reading a couple articles, Test-Driven Development (TDD) is the process by which you write tests for your code to make sure that it works as expected. You do this even before you start to write a single line of code and then you allow your code to adapt to the test and make it pass. The benefit of it is that it helps keep your code clean and do what it’s supposed to do. With a well-written test, you can refactor your code safely over and over knowing that the functionality has not been broken.

*Behavior*-Driven Development (or BDD) is a branch of TDD. I find it really hard to figure out the difference between the two aside from the fact that BDD has a much easier to read syntax than TDD. It’s almost like a human-readable sentence! From the articles that I’ve read and the people I’ve asked, they said that BDD is almost like user stories. BDD tests the functionality as a whole, compared to TDD, which only tests a single unit or function (which is likely to be a part of the overall behaviour itself)

## Test-Driven Development with Mocha + Chai
#### What is Mocha?
[Mocha](http://mochajs.org/#installation) is one of the popular Javascript testing framework out there. It provides us with an environment from which we can automate our tests. And that environment happens to run on NodeJS.

#### What is Chai?
To write a test, we need additional plugins like Chai. Chai is an assertion library. You can think of an assertion library as a language that is spoken inside a testing framework (in our case, Mocha). You can still use vanilla JS inside a testing framework though, for example, when declaring variables and functions, but to actually do the “testing”, you have to switch to the proper language. There are many assertion libraries out there, as you can see from [Mocha’s Assertions](http://mochajs.org/#assertions) section.

I’m a bit biased in the sense that this is the testing framework and assertion library that we use at [Small Improvements](https://www.small-improvements.com/), so I wanted to learn more about it and hopefully be able to contribute on this part of the codebase in the near future. But just a few google searches alone and you’ll see that there are many more frameworks out there such as [Ava](https://github.com/avajs/ava), [Sinon](http://sinonjs.org/), [Tape](https://github.com/substack/tape) and [Jasmine](https://jasmine.github.io/). I haven’t actually tried them myself but I’m going to keep them at the back of my mind when building my next personal project.

#### Prerequisites
This tutorial assumes that you already have a project folder set up with npm and Node installed. At the time of writing, I’m using a node version ____ and an npm version ____

{% highlight javascript %}
// a very simple folder structure
|_ package.json
|_ index.js
|_ test.js
{% endhighlight %}

#### Installation
Installing a testing framework is just as easy as installing as another framework through npm:

On the code below, we’ll install Mocha and Chai as a local dependency on our project
`npm install --save-dev mocha chai`


## Warm up: Writing our first test
For our Mocha interface, we will use [BDD system](http://mochajs.org/#interfaces) for ease of readability. The BDD system will provide us with chainable functions such as `describe()`, `it()`, and `before()`.

First, inside `test.js`, we want to *describe* what we are testing.

{% highlight javascript %}
describe('my function', function(){
  ...
});
{% endhighlight %}

The first argument is a string that describes the functionality that you’re testing. Ideally, its the function’s name but you can name it whatever you want. It’s a reference for you, so that when you run your tests later, you know exactly what they are.

Inside the describe block, we want to test the functionality that our function has to fulfill. We require [Chai’s assert interface](http://chaijs.com/api/assert/) at the top of the file and access its `strictEqual` method to compare two values. For now, let’s make our test fail.

{% highlight javascript %}
var assert = require('chai').assert;

describe('my function', function(){
  it('should be equal', function(){
    var isTrue = true;
    assert.strictEqual(isTrue, false);
  });
});
{% endhighlight %}

## Running tests
In our package.json, you can change the `tests` script to run mocha:

{% highlight javascript %}
"scripts": {
  "test": "mocha"
},
{% endhighlight %}

and then on the Terminal, you can simply run `npm test`

![A failed test]({{ site.url }}/assets/img/failed-test.png)

Once you’ve run the command, you’ll see that your test failed because, well, your values are not strictly (===) equal. You can change either value and run the command again to see how a successful test looks like!


## Replicating FreeCodeCamp’s PigLatin Test
Now that we have a basic idea on how to write tests using Mocha and Chai, let’s step our game up a bit and replicate the [PigLatin challenge on FCC](https://www.freecodecamp.org/challenges/pig-latin).

{% highlight javascript %}
// index.js
function pigLatin(str) {
  function check(obj) {
    return ['a','i','u','e','o']
      .indexOf(str.charAt(obj)) == -1 ?
      check(obj + 1) : obj;
  }

  return str.substr(check(0))
    .concat((check(0) === 0 ? 'w' :
    str.substr(0, check(0))) + 'ay');
}

module.exports = pigLatin; // export the function so we can import it somewhere else
{% endhighlight %}

{% highlight javascript %}
// test.js
var assert = require('chai').assert;
var pigLatin = require('./index');

var california;
var algorithm;

describe('piglatin', function(){
  // we call the function inside the `before` block before we test it for equality
  before(function() {
    california = pigLatin('california');
    algorithm = pigLatin('algorithm');
  });

  it('should return a string', function() {
    var test = pigLatin('hello');
    assert.isString(test);
  });

  it('should be aliforniacay', function() {
    var answer = 'aliforniacay';
    assert.strictEqual(california, answer);
  });

  it('should be algorithmway', function() {
    var answer = 'algorithmway';
    assert.strictEqual(algorithm, answer);
  });
});
{% endhighlight %}

![A passing test]({{ site.url }}/assets/img/passing-test.png)

## Conclusion
We scratched the surface of Test-Driven Development and implemented tests that challenged our understanding.

Writing a good tests are important for every codebase. It ensures that we deliver as little buggy software to our users as possible. At first I thought that TDD is a very big and daunting task, but it turned out to be really fun!

What are you going to test next?
