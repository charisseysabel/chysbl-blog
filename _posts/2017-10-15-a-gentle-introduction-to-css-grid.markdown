---
layout: post
title:  "A Gentle Introduction to CSS Grid"
date:   2017-10-15 09:00:00 +0200
categories: tutorial
---

I wanted to take a look at CSS Grid for quite a while, so I rebuilt an old project using it.

CSS Grids allow us to design a layout in a 2-dimensional style: horizontally and vertically. Unlike flexbox, which is only focused on only 1 dimension.

I must admit that I was a bit hesitant about the idea of Grid because of the fact that flexbox has solved the problems that I faced with layouts today (at least most of them anyway).

#### But what exactly is the difference between CSS Grid and flex box?
Rachel Andrew’s example was a good reference point for me to understand the difference. Basically, a layout that does not depend on the content’s width and height can benefit from a Grid layout. While a layout that needs its contents to flow and stretch would most likely have to use flexbox instead. But who says you can’t use them both at the same time?

As a simple exercise, I want to rebuild my FCC project: Quote Generator. Currently this was built using absolute positioning, which is fine but not exactly that elegant. It’s worth looking back at it and see how CSS Grid can make a difference.

Imagine adding an invisible grid on top of this layout. In the graphic design world, this is like activating the rulers inside Photoshop, which is what we usually do when it comes to aligning our items perfectly on the page. We create columns to align everything vertically, and set rows to align items horizontally.

![Setting guides... you know what I mean]({{ site.url }}/assets/img/with-guides.png)

#### How do we replicate this in code?
If you’ve used Flexbox before, then its helpful to think that it works pretty much the same for Grid: enclosing a group of elements together inside a grid display activates a bunch of other attributes that can only be used inside the grid container.

{% highlight html %}
<div class="grid-container">
    <div class="grid-item">
        <p>A reader lives a thousand lives before he dies, the man who never reads lives only one.</p>
        <span>Alison Croggon, the Naming</span>
    </div>
</div>
{% endhighlight %}

{% highlight css %}
.grid-container {
    display: grid;
}
{% endhighlight %}

Next, we can define our columns and rows using `grid-template-columns` and `grid-template-rows` which take a space-separated list of values. In our mock example, I’m setting the leftmost column to `300 pixels` and let the second column occupy the rest. You can set as many as you want depending on how many elements you have inside the grid-container. If you set less values than elements, the extra elements will automatically wrap and create a new row, following the same format as the row above it.

I’m also setting the topmost row to be `200px` high and let the second column occupy the rest of the space.

{% highlight css %}
.grid-container {
    display: grid;
    grid-template-column: 300px auto;
    grid-template-rows: 200px auto;
}
{% endhighlight %}

![Visualizing the grid]({{ site.url }}/assets/img/with-grid.png)

A grid layout also wouldn’t be complete if there’s no way to add gaps between the items. We can use `grid-row-gap` and `grid-column-gap` to do just that. This might be useful should we add content to our currently-empty sidebar in the future. I like this feature a lot because it helps avoid the use of a container padding altogether.

Optionally, we can use `grid-gap` as a shorthand notation for the 2 attributes above.

Now, all that’s left is to explicitly position our quote at the bottom-right of our grid. We can do this using `grid-column` and `grid-row`. In the code below, we specify our item to start at grid line #2 (setting only one value makes the element stretch all the way to the last column). We do the same for the row position.

![Grid and column lines]({{ site.url }}/assets/img/grid-columns-and-rows.png)

{% highlight css %}
.grid-item {
    grid-column: 2;
    grid-row: 2;
}
{% endhighlight %}

#### What about responsiveness?
Responsiveness can be achieved through the combination of media queries and modifying our `grid-template-columns` into a single value, forcing the elements to align vertically and re-arrange our grid-item’s columns and rows accordingly. It’s not very elegant, but it does the job.

#### Conclusion
CSS Grids allow us to design a layout in 2 dimensions: horizontally and vertically. A layout that does not depend on the content’s width and height can most certainly benefit from using Grid. Despite it being not entirely supported yet, especially in the older versions of IE, CSS Grid gives us an entirely new way of building layout for our websites. Definitely worth keeping an eye out for!
