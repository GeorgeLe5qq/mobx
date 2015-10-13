# Concepts & Principles

## Concepts

Mobservable distinguishes the following concepts in your application. You saw them in the previous gist, but let's dive into them in a bit more detail.

### 1. State

_State_ is the data that drives your application.
Usually there is _domain specific state_ like a list of todo items and there is _view state_ such as the currently selected element.
Remember, state is like spreadsheets cells that hold a value.

### 2. Views

_Views_ are all forms of data that can be derived from the _state_ without any further interaction.
Views exist in many forms:

* The _user interface_.
* _Derived data_, such as the amount of todos left.
* _Backend integrations_ like sending changes to the server.

Views are characterized by the fact that they never change the _state_.
Views are like spreadsheet cells that hold a formula.

### 3. Actions

An _action_ is any piece of code that changes the _state_. User events, backend data pushes, scheduled events etc.
An action is like a user that enters a new value in a spreadsheet cell.

## Principles

Mobservable supports an uni-directional data flow where _actions_ changes the _state_, which in turn updates all affected _views_.

![Action, State, View](../images/action-state-view.png)

_Views_ are updated **atomically** when the _state_ changes. As a result it is not possible to ever observe intermediate values.

_Views_ are updated **synchonously** by default. This means that for example _actions_ can safely inspect a _view_ directly after altering the _state_.

_Views_ are updated **lazily**. A View that is not actively in use will not be updated.
This largely avoids the need to dispose views.
If a view is no longer in use it will be garbage collected automatically.

_autorun_ creates a special kind of views. They evaluate eagerly instead of lazily. But without producing a value.
_autorun_ functions if you have _views_ that should always be updated, even if it they are not in use by any other view.
This is useful to achieve **effects**; code that kicks off some imperative code if the _state_ changed.
Stuff like logging to the console, updating the UI or sending changes to the server.

All _views_ should be **pure**. They are not supposed to change _state_.

## Illustration

The following listing illustrates the above concepts and principles:

```javascript
import {observable, autorun} from 'mobservable';

var todoStore = observable({
	/* some observable state */
	todos: [],

	/* a reactive view */
	completedCount: function() {
		return this.todos.filter(() => todo.completed).length;
	}
});

/* a function that observes the state */
autorun(function() {
	console.log("Completed %d of %d items",
		todoStore.completedCount,
		todoStore.todos.length
	);
});

/* ..and some actions that modify the state */
todoStore.todos[0] = {
	title: "Take a walk",
	completed: false
};
// -> synchronously prints 'Completed 0 of 1 items'

todoStore.todos[0].completed = true;
// -> synchronously prints 'Completed 1 of 1 items'

```

In the [5 minute introduction to Mobservable and React](https://mweststrate.github.io/mobservable/getting-started.html) you can dive deeper into this example and build an user interface using [React](https://facebook.github.io/react/) around it.