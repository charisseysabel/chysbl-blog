---
layout: post
title: "Generate Accessible Color Palettes with CSS Custom Properties"
date: 2018-02-17 09:00:00 +0200
categories: tutorial
---

I’ve heard of CSS Custom Properties before, although I didn’t think about using it until I came across a problem: how can I change styles dynamically if CSS Preprocessors do not support it?

## Motivation
One of the most convenient features of a CSS Preprocessors is the fact that you can create variables, much like the same way as how you declare a variable in other programming languages. It makes our lives easier by following the DRY principle. You can even create an entire file made up of just variables and reference it everywhere! But one catch is that CSS preprocessors needs to be compiled first; you can’t change the values dynamically with JS.

After attending Beyond Tellerrand conference in Berlin last November 2017, I got very inspired to learn more and take action to make our application accessible after watching a [talk by Robin Christopherson](https://beyondtellerrand.com/events/berlin-2017/speakers/robin-christopherson#talk). He showed us how disabled people like him (he’s blind and uses Assistive Technology) use the web. It was an eye-opening experience.

Even though the use of CSS Custom Properties is not a solution that fits all, I think we can still take advantage of it to create an option for users to opt into a more accessible colour palettes if they want to, while still allow websites/companies to stick to their brand colours by default. It’s a win-win solution, I think.

In the end, it’s not just about making an application accessible, but its also a chance to personalise the applications that we use. To make it more “our style”. I've seen it already in many other applications. Whenever I read an article on Pocket for example, I sometimes prefer to read it on a dark background just because I want to.

Another benefit of using CSS Custom Properties is that, it does not require much refactoring, (at least in my opinion). A website or application can have as much as hundreds of thousands of lines of code for styles alone! A framework can be a good option if you’re just starting from the ground up, but it can be quite expensive to spend a month refactoring style sheets. And I’m not sure anyone would enjoy doing that.

## Prerequisite
I’m going to skip the basics of CSS Custom Properties because there’s tons of tutorials online about it already.

Also, the following code snippets will be using React, so some knowledge of that would be good too.

## Declare the variables
The first thing that we’re going to do is to declare our variables in the `:root` pseudo-selector, although its not limited to that. CSS Custom Properties can be declared inside any selector. But unlike preprocessor variables, they cannot be declared on their own.

{% highlight css %}
/* valid */
:root {
  --brandSalmonColor: salmon;
}

/* valid */
.container {
  --brandTealColor: teal;
  background-color: var(--brandTealColor);
}

/* invalid */
--standardPadding: 20px;
{% endhighlight %}

If you have a file with all of your preprocessor variables, you can simply migrate them and declare them onto the `:root` pseudo-selector. In my opinion, it is nice to have them all available but it also creates unnecessary clutter on the global namespace so I would suggest to limit the amount of variables that will be declared in the `:root` pseudo-selector and declare other variables in a scope instead.

## Change colours dynamically
Suppose that you have a text on a colored background like so:

<p data-height="265" data-theme-id="0" data-slug-hash="eVyGLY" data-default-tab="result" data-user="charisseysabel" data-embed-version="2" data-pen-title="Contrast Check Fail" class="codepen">See the Pen <a href="https://codepen.io/charisseysabel/pen/eVyGLY/">Contrast Check Fail</a> by Charisse (<a href="https://codepen.io/charisseysabel">@charisseysabel</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

If we analyse it using the [colour contrast checker](https://webaim.org/resources/contrastchecker/), we realise that the colour palette doesn't reach any of the suggested contrast levels (no matter how much we justify that pink salmon is beautiful).

Once we already have the default variables in place, we can come up with an alternative colour palette that has a higher contrast by playing with the colours in the contrast checker. Then we can change the theme when an event has occurred.

In this example, we want to enable a high contrast mode when the user clicks on the button.

{% highlight javascript %}
// App.js
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  constructor() {
    super();
    this.changeColor = this.changeColor.bind(this);
  }

  // very simple example but you get the point
  changeColor() {
    const color = document.documentElement.style.getPropertyValue('--textColor');
    const value = (color === 'white' || color === '') ? 'black' : 'white';

    document.documentElement.style.setProperty('--textColor', value);
  }

  render() {
    return (
      <div className="App">
        <header className="App-header">
          <h1 className="App-title">Banner text</h1>
          <button onClick={this.changeColor}
            className="App-button">
            Enable High Contrast Mode
          </button>
        </header>
      </div>
    );
  }
}

export default App;
{% endhighlight %}

{% highlight css %}
/* App.css */
:root {
  --bannerBg: salmon;
  --textColor: white;
}

.App-header {
  background-color: var(--bannerBg);
  height: 150px;
  padding: 20px;
  color: white;
}

.App-title {
  font-size: 1.5em;
  color: var(--textColor);
}

.App-button {
  padding: 10px;
  border-radius: 3px;
}
{% endhighlight %}

## Persist Configuration
We have all the mechanism in place but whenever we refresh the page, the configuration is gone. At this point, I thought of using localStorage. Its a cheap and easy way to save the configuration. Caching is probably a better solution, but I have yet to learn that so I’ll stick with localStorage for now.

We also have to make sure that when the component mounts, it should check the localStorage first and apply the configuration if its available.

{% highlight javascript %}
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  constructor() {
    super();
    this.changeColor = this.changeColor.bind(this);
  }

  // make sure that localStorage is actually available and usable
  // and a configuration was actually saved in storage.
  componentWillMount() {
    if(window.localStorage && window.localStorage.length > 0) {
      document.documentElement.style.setProperty('--textColor', window.localStorage.getItem('textColor'));
    }
  }

  changeColor() {
    const storage = window.localStorage;
    const color = storage.getItem('textColor') === 'white' ? 'black' : 'white';

    // override color in localStorage and assign the new color to the CSS Custom Property
    storage.setItem('textColor', color);
    document.documentElement.style.setProperty('--textColor', storage.getItem('textColor'));
  }

  render() {
    return (
      <div className="App">
        <header className="App-header">
          <h1 className="App-title">Banner text</h1>
          <button onClick={this.changeColor}
            className="App-button">
            Enable Color Contrast Mode
          </button>
        </header>
      </div>
    );
   }
}

export default App;
{% endhighlight %}

Now when we refresh the page, the colors -- whether in high contrast mode or not -- should persist.


## Final Thoughts
I personally really like this simple solution to solve the problem of inaccessible colours. Its a small project, but it was really fun and doesn’t require too much overhead. I am looking forward to explore this topic again in the future, maybe to cache the theme instead of using localStorage, or use a different technology altogether.
