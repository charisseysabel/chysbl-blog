---
layout: post
title:  "Up and Running with Webpack"
date:   2017-07-03 09:34:24 +0200
categories: tutorial
---

One of the most important things you have to have in your toolkit these days is the ability to use [Webpack](https://webpack.js.org/) — and for a good reason. Webpack is a module bundler. It takes your assets — your JS files, CSS, images, etc — and outputs it as a single file, saving you with tons of trips back and forth from the server.

I gotta admit, the first time I’ve set up my own Webpack configuration file, I was pretty stoked and nervous and overwhelmed at the same time. But its important to know that Webpack is just another javascript file. It’s just a configuration file that you set up and run on the command line that will automatically does what you tell it to do. It is incredibly powerful, so its worth investing some time learning it.

In this post, we’ll set up a basic webpack configuration file.

## Install Node and NPM
To get started, we have to install [Node.js](https://nodejs.org/en/) first. Node.js (or simply called “Node”) is an API that allows you to use Javascript on the server. We need to install it so that we can access [NPM](https://www.npmjs.com/) (or Node Package Manager). According to the Node.js website, NPM is the largest ecosystem of open source libraries in the world — and that includes Webpack. If you’re pulling your hair out trying to implement a solution for your problem, look around in the npm website; a library might already exist that solves the same problem that you have.
NPM comes with the Node.js installer but make sure that you have the latest stable version of npm:

`sudo npm install npm@latest -g`
(You might have to omit “sudo” if you’re using Windows)
Note: the `-g` flag allows you to install npm “globally”, meaning you don’t need to install it again if you create another project because its readily available in your machine. Omitting the flag only installs it in the local folder where you ran the command.

To check the version of npm that you have, run `npm -v` and, `​node -v` to check the version of node. At the time of this writing, I’m using v5.0.3 of NPM and v8.1.2 of Node.

#### Create your package.json file
Once you’re in your project folder, run (`npm init`)[https://docs.npmjs.com/cli/init]
This creates a file called `package.json` for you. It will ask you a bunch of questions about your project but you can just simply skip through them by pressing enter. Alternatively, you can skip the questions entirely by adding the `-y || --yes` flag.

So what exactly is a `package.json` file? Imagine that you want to upload your work on GitHub or you simply want to distribute your app. It doesn’t make sense to upload all your app’s dependencies* on GitHub because that can be a ridiculously huge list and you don’t want other developers to manually install those dependencies themselves. What `package.json` does is that it helps you keep track of your application’s dependencies. You can then upload this single file to GitHub and anyone who wants to run your application locally can just refer to it and run a simple `npm install` to install everything in one go.

*Dependencies are files or libraries that your app “depend” on. Like, it literally can’t run without it. If you used jquery in your code, then you could say that you have jquery as a dependency. Numerous libraries these days depend on other libraries to work properly, so its always nice just to be able to skip the headache of managing those manually.


## Install webpack
Next we install Webpack:
`npm install --save-dev webpack`
Using the `--save-dev` flag allows you to save it in your `package.json` under the devDependencies object.

The very first dependency that you install will always create a node_modules folder in your project’s root folder. In this folder, you will find webpack and all the other libraries that it depends on (and its a LOT!). Every other libraries that you install via NPM will be dumped here from now on.

This is how our folder structure looks like so far:

{% highlight javascript %}
root
|_ index.html
|_ index.js
|_ node_modules/
|_ package-lock.json
|_ package.json
{% endhighlight %}

{% highlight javascript %}
// index.js:
document.write('Hello World!');
{% endhighlight %}

{% highlight html %}
// typical copy-pasted index.html file
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!--[if lt IE 9]>
      <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
      <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
    <![endif]-->
    <title>Up and Running with Webpack</title>
</head>
<body>
    <div id='app'></div>
</body>
</html>
{% endhighlight %}


## The 4 concepts of Webpack
Now that we have Webpack installed, let’s start configuring it! You can check for the version of webpack that you're using by running npm list webpack on the terminal. At the time of writing, I'm using v2.6.1.

Webpack has four main concepts: Entry, Output, Loaders, and Plugins. We’ll tackle them one by one.

#### Entry
Start by creating a `webpack.config.js` and write the code below:
{% highlight javascript %}
var webpack = require('webpack');
module.exports = {
  entry: './path/to/entry/file.js',
};
{% endhighlight %}

The `require` function and the `module` object comes with Node and is a part of the CommonJS API:

* The `require` function allows us to import a module (or a file) into the current scope.
* The `module` object allows us to export something from the current scope.

In order for Webpack to work, we need to specify an entry point. Usually its the root `index.js` file but it can be whatever filename in whatever directory you like.

#### Output
Whenever we tell Webpack to bundle our assets, it also needs to know where the bundled application should live. For that, we need to specify an output property inside the `module.exports` object.

{% highlight javascript %}
var webpack = require('webpack');
var path =  require('path');
module.exports = {
	entry: './path/to/entry/file.js',
	output: {
		path: path.resolve(__dirname, 'dist'),
		filename: 'bundle.js'
	}
};
{% endhighlight %}

We first require the [`path`](https://nodejs.org/api/path.html) module (which is also included in Node) so that it can resolve the file and directory paths for us. Then we use its [`resolve`](https://nodejs.org/api/path.html#path_path_resolve_paths) method to process the absolute path. The [`__dirname`](https://nodejs.org/api/globals.html#globals_dirname) returns the directory name of the current module.

The `filename` property let’s us specify the name of the file of the bundled application.
So in the code above, we say, find the `dist` directory in the current module and add a file called `bundle.js`. We don’t have this file and folder created yet, but Webpack can do it for us.

#### Loaders
Next, we define the Loaders. Loaders are the transformations that are applied on your code. Let’s say you use a CSS preprocessor like LESS or SASS. By default, the browser doesn’t understand its syntax, so you need a specific loader that will compile these `.less` or `.scss` files into normal CSS so that the browser can understand it.

There are [different loaders available](https://webpack.js.org/loaders/) and you can install them through NPM as well:
`npm install --save-dev loader-name`

So, in the example below, we tell webpack that for every `.less` file that it encounters (handled by the regular expression), apply these transformations.

(I omitted the previous code that we wrote before for brevity)
{% highlight javascript %}
...
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.less$/,
        use: [{
          loader: style-loader,
        }, {
          loader: 'css-loader',
        }, {
          loader: 'less-loader',
        }],
      },
    ],
  },
};
{% endhighlight %}

Note: after some trial and error, I found that the loaders are applied from bottom up. So the less-loader is applied first. You can read about these loaders in the [Webpack documentation](https://webpack.js.org/loaders/).
You can test a variety of other files such as your JS, svg, png, etc files and run a specific loader for each one.

#### Plugins
According to the webpack documentation, plugins are “most commonly used (but not limited to) performing actions and custom functionality on chunks of bundled modules”.

To put it simply: Once you’ve bundled your assets, you can do a lot of other things with those bundled assets.

The most common plugin that I use is [`HtmlWebpackPlugin`](https://www.npmjs.com/package/html-webpack-plugin) that you can install via npm. This plugin creates an HTML file(s) that will serve your webpack bundles:

`npm install -save-dev html-wepback-plugin`

Plugins, unlike loaders, need to be required at the beginning of the file:

{% highlight javascript %}
var htmlWebpackPlugin = require('html-webpack-plugin');
...
{% endhighlight %}

Then, in your `module.exports` object, create a new instance of HtmlWebpackPlugin object inside the plugins array:
{% highlight javascript %}
...
  module.exports = {
  ...
    plugins: [
      new HtmlWebpackPlugin({
        template: './index.html',
        inject: 'body',
      })
    ]
  };
{% endhighlight %}

Here, I specified a configuration object containing a template property inside the new HtmlWebpackPlugin instance. When I run webpack on the command line, HtmlWebpackPlugin takes the template HTML file and creates a new one in the `dist` folder. This new HTML file will contain our bundled assets injected at the end of the `body` tag.

## Running Webpack
There are a couple of ways to run webpack and one of them (and probably the most convenient one) is to add a script in your `package.json` file. We don’t really need the `test` key in the scripts object so we can just replace it.

// package.json
{% highlight json %}
"scripts": {
	"production": "webpack -p",
}
{% endhighlight %}

Then, in the terminal, run:
`npm run production`
Then, in the terminal, run `npm run production`. This creates a production-ready file for your assets, meaning its all compiled and minified!

#### Bonus: Webpack Dev Server
Probably my most favourite part of webpack (so far) is the development server. To put it simply, the webpack development server provides you with a server and live reloading.

Live reloading is cool because the browser reloads or refreshes the entire app every time a file changes. You don’t have to manually do it yourself!


#### Setting up the dev server
Install webpack dev server from npm:
`npm install --save-dev webpack-dev-server`

Then, add another script in your `package.json` file:
`"start": "webpack-dev-server --open"`
The `--open` flag lets you automatically open the browser on `http://localhost:8080`
Now, when you run `npm run start`, it should open a browser for you and show you a page with the "Hello World" text on it!


## Conclusion
And there you have it! This setup is pretty basic but there’s already so much you can do with this simple configuration. Webpack is a very powerful module bundler and it does tons of other awesome things so I recommend that you check out their documentation, play around and have fun!

#### Where to go from here
Try fiddling around with Webpack a little more by setting up a configuration for your JS files. For example, if you want to use the [`ES6 syntax`](http://es6-features.org/#Constants), you have to install a couple of loaders from [`Babel`](https://babeljs.io/) via npm. If you want to use a library or a framework, just read their docs; they usually have handy sections on how to get started.






