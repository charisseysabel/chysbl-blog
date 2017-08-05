---
layout: post
title:  "The Grunt Task Runner"
date:   2017-08-05 12:00:00 +0200
categories: tutorial
---

Task runners are great for automating tasks. Small, repetitive tasks such as minifying, uglifying or even just watching our files for changes can easily take up our time.

I’ve read some articles online that says automation was once done using shell scripts. I haven’t really tried that myself (but now I’m really curious) but now that frontend technologies have evolved drastically these past few years, task runners like [Gulp](https://gulpjs.com/) and [Grunt](https://gruntjs.com/) took our frontend environment another step forward.

In this post, I experimented about the basic Grunt setup and learned how to automate a task like compiling LESS files to normal CSS and watching for file changes. I didn't really have a special preference whether to use Grunt or Gulp. I haven't used any of them in any of my projects so I just picked one and moved on.

## Prerequisites

Before we get started, make sure you have the latest version of Node and NPM. At the time of writing, I am using Node v8.2.1 and NPM v5.3.0. Further in the tutorial, I will assume that you have a npm project installed through `npm init`.


{% highlight javascript %}
Folder Structure
|_ node_modules/
|_ assets/
   |_ main.less
|_ Gruntfile.js
|_ package.json
|_ package-lock.json
{% endhighlight %}

## Installation
So the first thing we have to do is install `grunt-cli`  globally by running: `npm install -g grunt-cli` This will allow us to run the version of Grunt that we will install in our project. The `grunt-cli` itself is not the task runner; its only the runner of the task runner :D

At first I only installed the `grunt-cli` locally on my project folder because for trying out new technologies, I really don’t want to have anything installed globally. But for some reason, it doesn’t work when its only installed locally which kinda sucks.

Next, we install `grunt` by running `npm install --save-dev grunt`. This will create a `node_modules` folder for us.

## Plugins
Grunt by itself does not do anything and chances are, if you need to use a task runner, then you probably have something that you want to automate. For a particular task, we need to install a different library, which Grunt refers to as a Plugin. You can find all the different plugins for Grunt in the [project’s plugins page](https://gruntjs.com/plugins).

As a first example, let’s say we want to use LESS in our code. Obviously we have to compile that into normal CSS so that the browser can understand it.

So we can go ahead and install the less plugin by running `npm install --save-dev grunt-contrib-less`.

Here’s an example LESS file that we want to compile:
{% highlight css %}
// main.less
@color: red;

h1 {
  color: @color;
}
{% endhighlight %}

## Gruntfile
Create a file called `Gruntfile.js` (note the capital G). In this file will live all configurations that we want Grunt to do for us.

In this file, we want to export a function, passing in an argument called `grunt`:

{% highlight javascript %}
// Gruntfile.js
module.exports = function(grunt) {}
{% endhighlight %}

There are 3 main methods that we want to access in the `grunt` object:
* `grunt.initConfig` takes an object that specifies the configuration for each of our desired plugin
* `grunt.loadNpmTasks` take strings indicating the plugin that we want to use
* `grunt.registerTasks` allows you to set the default task for grunt

Now that I have my LESS plugin installed, I can enable it inside my Gruntfile by calling the `loadNpmTasks` method and passing it the name of our plugin:

{% highlight javascript %}
// Gruntfile.js
module.exports = function(grunt) {
  grunt.loadNpmTasks('grunt-contrib-less');
}
{% endhighlight %}

#### The LESS plugin
Next, we access grunt’s `initConfig` method where we pass an object that contains the configuration for the tasks that we want to automate. Note that every plugin might have a different way of configuration so its best to refer to their documentation!

Here, we specify a `development` object for our LESS task:

{% highlight javascript %}
// Gruntfile.js
module.exports = function(grunt) {
  grunt.initConfig({
    less: {
      development: {
        options: {
          paths: ['./assets/main.less'],
        },
        files: { './build/main.css': './assets/main.less' },
      }
    }
});
  grunt.loadNpmTasks('grunt-contrib-less');
}
{% endhighlight %}

 `options`  take `paths` where we specify an array, a single string, or a function that leads to our source LESS file(s). There are other options that we can add but we’ll keep it simple for this example. Alternatively, you can omit this object entirely if you don’t have additional options; it will work just the same.

`files`  take an object with its key pointing to the resulting CSS file and its value being the path to the source LESS file.

Now if you run `grunt less` on the command line, you will see that our task runner will compile our LESS file and create a `build` folder for us, containing our compiled CSS file!

The `registerTasks` method allow us to specify what tasks to run if there are no tasks specified, for example if you just type `grunt` and not `grunt less`. This method takes 2 arguments: an alias (or a name for a task, which is called `default` by default) and an array of tasks.

So in the code below, we could say that we want our LESS files to compile whenever we run `grunt` :

{% highlight javascript %}
// Gruntfile.js
module.exports = function(grunt) {
  grunt.initConfig({...});
  grunt.loadNpmTasks(...);
  grunt.registerTask('default', ['less']);
}
{% endhighlight %}

So, if you run `grunt` on the command line, it will do the same as `grunt less`

Once you have configured all the tasks that you want to automate, it would be nice if we don’t have to repeatedly run `grunt` everytime we edit / add / delete files. The solution, of course, is to automate the task runner itself!

#### The Watch plugin
Start by installing `grunt-contrib-watch` and save it to your devDependencies.

Once its installed, enable it inside your Gruntfile by adding another `loadNpmTask`  and register it as the default task instead of  `less` that we defined earlier.

{% highlight javascript %}
// Gruntfile.js
module.exports = function(grunt) {
  grunt.initConfig({..});
  grunt.loadNpmTasks('grunt-contrib-watch');
  grunt.loadNpmTasks('grunt-contrib-less');
  grunt.registerTask('default', ['watch']);
}
{% endhighlight %}

Just like the previous task, we will configure the `watch` task inside the  `grunt.initConfig` method:

{% highlight javascript %}
// Gruntfile.js
grunt.initConfig({
  watch: {
    // config for the watch task goes here
  },
    ... // other tasks
});
...
{% endhighlight %}

Inside `watch` , we group tasks based on the things we want to watch for, say, the “styles” object for all the styling (it can be named anything you want). We then specify the files we want to watch for inside the `files` inside “styles”. In the code below, we are recursively telling Grunt to watch files with a `.less` extension in folders and its subfolders.
Once Grunt sees that our `.less` files have changed, its going to run the `less` task that we’ve specified before.

{% highlight javascript %}
// inside the watch object
watch: {
  styles: {
    files: '/**/*.less',
    tasks: ['less'],
  }
}
{% endhighlight %}

Go ahead and run `grunt` and change the `main.less` file. Once you hit save, you’ll see that the `less` task ran and updated our code! Pretty cool right? :D

## Where to go from here
Here’s the complete code snippet so far:

{% highlight javascript %}
// Gruntfile.js
module.exports = function(grunt) {
  grunt.initConfig({
    less: {
      development: {
        options: {
          paths: ['./assets/main.less']
        },
        files: { './build/main.css': './assets/main.less' },
      }
    },
    watch: {
      styles: {
        files: '/**/*.less',
        tasks: ['less'],
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-watch');
  grunt.loadNpmTasks('grunt-contrib-less');
  grunt.registerTask('default', ['watch']);
};
{% endhighlight %}

If you’re working on a modern front end environment, chances are, you’re also dealing with Javascript.

Find a way to minify and concatenate your files. Look for the right plugin and read their documentation. If you’re stuck, search or ask questions on Stack Overflow. Your first few tries might not work and might leave you frustrated. This is natural and the only way to get past it is to keep going and remember to have fun!
