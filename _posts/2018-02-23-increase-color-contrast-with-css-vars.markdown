---
layout: post
title: "Increase Color Contrast in React with CSS Custom Properties"
date: 2018-02-23 09:00:00 +0200
categories: tutorial
---

I’ve heard of CSS Custom Properties before, although I didn’t think about using it until I came across a problem: how can I change styles dynamically if CSS Preprocessors do not support it?

## Motivation
One of the most convenient features of a CSS Preprocessors is the fact that you can create variables, much like the same way as how you declare a variable in other programming languages. It makes our lives easier by following the DRY principle. You can even create an entire file made up of just variables and reference it everywhere! But one catch is that CSS preprocessors needs to be compiled first; you can’t change the values dynamically with JS.

After attending Beyond Tellerrand conference in Berlin last November 2017, I got very inspired to learn more and take action to make our application accessible after watching a [talk by Robin Christopherson](https://beyondtellerrand.com/events/berlin-2017/speakers/robin-christopherson#talk). He showed us how disabled people like him uses the web. It was an eye-opening experience.

Even though the use of CSS Custom Properties is not a solution that fits all, I think we can still take advantage of it to create an option for users to opt into a more accessible colour palettes if they want to, while still allow websites/companies to stick to their brand colours by default. It’s a win-win solution, I think.

In the end, it’s not just about making an application accessible, but its also a chance to personalise the applications that we use. To make it more “our style”, so to say. I've seen it already in many other applications. Whenever I read an article on Pocket for example, I sometimes prefer to read it on a dark background just because I want to.

Another benefit of using CSS Custom Properties is that, it does not require much refactoring, (at least in my opinion). A website or application can have as much as hundreds of thousands of lines of code for styles alone! A framework can be a good option if you’re just starting from the ground up, but it can be quite expensive to spend a month refactoring style sheets. And I’m not sure anyone would enjoy doing that.

## Prerequisite
I’m going to skip the basics of CSS Custom Properties because there’s tons of tutorials online about it already.

Also, the following code snippets will be using React, so some knowledge of that would be good too. I used `create-react-app` for this short tutorial.

## Declare the variables
The first thing that we’re going to do is to declare our variables in the `:root` pseudo-selector. It's not limited to that, though. CSS Custom Properties can be declared inside any selector. But unlike preprocessor variables, they cannot be declared on their own.

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

If you have a file with all of your preprocessor variables, you can simply migrate them and declare them onto the `:root` pseudo-selector. In my opinion, it is nice to have them all available but it also creates unnecessary clutter on the global namespace. I find that whenever I inspect the styles on the browser console, every single element inherits the variables that have been declared in the `:root`. I guess it's fine if there's only a couple of variables declared, but I would suggest to limit the amount of variables that will be declared in the `:root` pseudo-selector and declare other variables in a scope instead.

## Low contrast example
Suppose that you have a text on a colored background like so:

<p data-height="265" data-theme-id="dark" data-slug-hash="eVyGLY" data-default-tab="result" data-user="charisseysabel" data-embed-version="2" data-pen-title="Contrast Check Fail" class="codepen">See the Pen <a href="https://codepen.io/charisseysabel/pen/eVyGLY/">Contrast Check Fail</a> by Charisse (<a href="https://codepen.io/charisseysabel">@charisseysabel</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

If we analyse it using the [colour contrast checker](https://webaim.org/resources/contrastchecker/), we realise that the colour palette doesn't reach any of the suggested contrast levels (no matter how much we justify that a light grey on white text is beautiful).

This is a problem -- but we already know that. Using low contrast colors affect legibility, discoverability and accessibility. So let's fix it.


## Fixing the damn thing
_Imagine at this point that your user somehow managed to find the Settings page in your application to activate the high contrast mode and now you just need to make the functionality work._

Once you've already migrated your preprocessor variables to your `:root` selector, we can now come up with an alternative colour palette that has a higher contrast by playing with the colours in the contrast checker. Let's say we want the high contrast version of our background color to be `#525252`. The tests will give us a sweet green passing tests when the high contrast mode is activated through a button click.

{% highlight javascript %}
// App.js
import React, { Component } from 'react';
import './App.css';

const MODE = {
  dark: {
    '--greyBg': '#525252',
  },
  light: {
    '--greyBg': '#ccc',
  }
}

class App extends Component {
  constructor() {
    super();
    this.state = {
      theme: 'light',
    }
    this.setTheme = this.setTheme.bind(this);
    this.updateColors = this.updateColors.bind(this);
  }

  setTheme(e) {
    this.setState({
      theme: e,
    })
    this.updateColors(e);
  }

  updateColors(theme) {
    Object.keys(MODE[theme]).forEach((color) => {
      document.documentElement.style.setProperty(color, MODE[theme][color]);
    })
  }

  render() {
    return (
      <div className="App">
        <header>
          <h1 className="App-title">Banner text</h1>
          <form>
            <label htmlFor="dark">High Contrast Mode</label>
            <input type="radio" name="theme" value="dark" id="dark" checked={this.state.theme === 'dark'} onChange={(e) => this.setTheme(e.target.value)}/>

            <label htmlFor="light">Default Mode</label>
            <input type="radio" name="theme" value="light" id="light" checked={this.state.theme === 'light'} onChange={(e) => this.setTheme(e.target.value)}/>
          </form>
        </header>
      </div>
    );
  }
}

export default App;
{% endhighlight %}

{% highlight css %}
:root {
  --greyBg: #ccc;
  --textColor: white;
}

.App {
  background-color: var(--greyBg);
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
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

## Persist configuration
We have all the mechanism in place but whenever we refresh the page, the configuration is gone. At this point, I thought of using localStorage. Its a cheap and easy way to save the configuration. The downside is that if you try to view the page in a different browser, our saved configuration won't take effect. Right now, I don't know what's the better way of storing such things, so I'll stick to localStorage for now.

When the component mounts, I want to check if the theme has already been saved in localStorage. If it's there, we call the `setTheme` method. Otherwise, we move on.
{% highlight javascript %}
// inside App component
...
componentDidMount() {
    if (window.localStorage && window.localStorage.getItem('theme') != undefined) {
      const theme = window.localStorage.getItem('theme');
      this.setTheme(theme);
    }
  }
...
{% endhighlight %}

We also need to update localStorage when we select between the two radio buttons. For this, we create a new method `updateStorage`
{% highlight javascript %}
// inside App component
...
updateColors(theme) {
  Object.keys(MODE[theme]).forEach((color) => {
    document.documentElement.style.setProperty(color, MODE[theme][color]);
  })
  this.updateStorage(theme);
}

updateStorage(theme) {
  if (!window.localStorage) {
    return;
  }

  // make sure that localStorage is actually available and usable
  const storage = window.localStorage;
  const storedTheme = storage.getItem('theme');
  if (storedTheme === theme) {
    return;
  }

  storage.setItem('theme', theme);
}
{% endhighlight %}

(You can check out the completed code from [this Gist](https://gist.github.com/charisseysabel/defbbe21b5a031d89f0c4aa4ba7dc1db))

Now when we refresh the page, the colors -- whether in high contrast mode or not -- should persist! We can also add as many colors as we want to update in the `MODE` object and every element that uses that variable will change. Yay!


## Final thoughts
I personally really like this simple solution to solve the problem of inaccessible colours. We used CSS Custom Properties to dynamically update the styles across the entire application where the custom property has been set and stored the theme in localStorage so that our browser can remember the configuration.

Its a small project, but it was really fun and doesn’t require too much overhead. I am looking forward to explore this topic again in the future, maybe to cache the theme instead of using localStorage, or use a different technology altogether.
