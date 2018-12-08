---
title: Backbone - avoiding memory leak
date: "2015-01-01"
category: 'js'
tags:
    - backbone
---

Today I will talk about some tips to avoid memory leaks when using Backbone code.

## Our code convention `destroy` and `teardown`

In Backbone View, we often create a `teardown` method to clean up memory. We call it as `destroy` method in Backbone Model.

## Use `listenTo` instead of `on` method to bind events

In `initialize` of View or Model, we often want to listen to events of other objects. Like this:

**Should not**

```js

var SomeModelView = Backbone.View.extend({
  initialize: function() {
    this.model.on('change', this.render, this);
  },
  render: function() {
    // render a template
  }
});

```

But since Backbone v0.9.x, we stop using `on` when binding the event handler. We should use `listenTo`.

** Should **

```js

...
initialize: function() {
    this.listenTo(this.model, 'change', this.render);
},
teardown: function() {
    this.remove(); // automatically remove bound events which uses listenTo() method.
},
...

```

Whenever we call `this.remove()`, the View will automatically unbind any event bound to it using the `this.listenTo()` method. For more detail, you can look at the `View.prototype.remove` method in Backbone code.

```js

 //in Backbone source code
...
// Remove this view by taking the element out of the DOM, and removing any
// applicable Backbone.Events listeners.
remove: function() {
      this.$el.remove();
      this.stopListening();
      return this;
},
...

```

That's it today. Happy coding!