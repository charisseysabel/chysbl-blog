---
layout: post
title: "Simple Trello Replica using React [Part 1]"
date: 2018-04-22 08:00:00 +0200
categories: tutorial
---

[Trello](https://trello.com/) has been one of my favorite productivity apps because of its straightforwardness and the amount of things that you can do with such a simple interface. I wanted to experiment, so I created a simple drag and drop component in React that imitates Trello's functionality using the HTML5 Drag and Drop API.

## Prerequisites
- Basic knowledge of React
- Node and NPM installed
- `create-react-app` for a fast setup.

## What's in the Drag and Drop API?
The [HTML Drag and Drop API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API) contains a lot of methods that are somewhat similar to each other. For my project, I used 4 events:

* `dragstart` (or `onDragStart` in React) is an event that fires when the user starts dragging an element. This event is attached to the element that will be dragged and has to be used in conjunction to the `draggable` attribute.
* `dragover` (or `onDragOver`) is an event that fires when a draggable element has hovered over a valid dropzone. This is useful if you want to dynamically add styles to the dropzone to give feedback to the user that the action is possible.
* `drop` (or `onDrop`) is an event that fires when a draggable element is dropped on a valid dropzone.
* `dragleave` (or `onDragLeave`) is fired when the element leaves a valid dropzone.

### The Data Transfer Object
The `DataTransfer` object holds the data that is being dropped during a drag and drop operation. It also holds properties such as `dropEffect`, which specifies the type of effect that will happen during a drag and drop operation. Since I am trying to replicate Trello's interface, a `move` dropEffect would suffice. I didn't really mess around with the other properties, but you can always learn more on [MDN](https://developer.mozilla.org/en-US/docs/Web/API/DataTransfer)


## Building the Components
If you were to break down a typical Trello board page, you would need a main `Board` page where all the components are going to mount on. I'm also going to add a simple "create-a-column" button where you can specify the name of the column and of course, a `Column` component that you can create multiple times and perform the drag and drop actions within them. Within those columns are smaller cards containing data.

### The Board Component
Now that we have all the information laid out, let’s start building!
Let’s first create the `Board` component. The `Board` component will be a stateful component that will be responsible for rendering the `Column`, `ColumnItem` and column-create button components.

We’ll keep track of how many `Column` components have been created through the state and render them one by one, otherwise, we’ll show some arbitrary message. For now, I want to focus on wiring everything up, and so the data will be static. We'll add the feature to add dynamic data later on.

<script src="https://gist.github.com/charisseysabel/95fa7b13ead3adb1a9c9d9110cd12ceb.js?file=Board.js"></script>

The above code will not work yet, as we have not created the `Column` component. So let’s do that now.

### The Column and ColumnItem Component
I wanted to make the `Column` component independent, meaning that it should not have to rely on the `Board` component in order for it to do its job of holding items, taking new items, and taking items out of it.

<script src="https://gist.github.com/charisseysabel/95fa7b13ead3adb1a9c9d9110cd12ceb.js?file=Column.js"></script>
<script src="https://gist.github.com/charisseysabel/95fa7b13ead3adb1a9c9d9110cd12ceb.js?file=ColumnItem.js"></script>

And finally, the styles:
<script src="https://gist.github.com/charisseysabel/95fa7b13ead3adb1a9c9d9110cd12ceb.js?file=App.css"></script>


Now, if you import the `Board` component into `App.js`, you should be able to add a new column and move the static items into any of the columns! It's not perfect but I'll definitely build upon it and improve it in the upcoming posts!

<script src="https://gist.github.com/charisseysabel/95fa7b13ead3adb1a9c9d9110cd12ceb.js?file=App.js"></script>

So far, the current implementation is not perfect, but I had a lot of fun building this and I will definitely improve upon it in the upcoming posts! What about you? Have you ever built a drag and drop interface before? How did you go about it?
