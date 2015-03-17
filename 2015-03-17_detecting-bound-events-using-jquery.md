---
title: How to detect for bound events in jQuery
tags: [jquery][javascript]
---

One of the great features that jQuery offers is the ability to add and remove events for DOM elements without much fuss.
All of it just works, cross-browser, cleanly.

However, as much as it's easy to add and remove events to any element, jQuery doesn't really offer a pretty way 
of detecting whether or not an element already has listeners subscribed to an event. 
This can be a bit problematic, because adding an event listener to an element that already has that same listener
bound will just result in multiple callbacks.

### Check out jQuery's internal recordkeeping

> Note : I must admit, this really feels a bit hacky. 

A lot of jQuery's internal data (what it keeps track of for you as you work with it) can be accessed via `jQuery._data()`.
For example, calling `jQuery._data(elem, 'events')` gets you event data one the specified elements.

> __Important__ : jQuery 1.11.2 marks this internal function as _deprecated_.

Event data is filed by jQuery as an object with properties corresponding to events.

```
{
  click : [...],
  hover : [...],
  ...
}
```

Each event property is an array of hashmaps, each containing data jQuery is maintaining
about a single handler bound to the event.

```
// :: jQuery._data(elem, 'events').click
{
  data : ...,
  handler : fn,
  namespace : ...,
  ...
}
```

Using this data, we can do all sorts of checks and filters to determine if an element has handlers we're interested in.

For example, to check if `<body>` has any `click` event handlers bound:

```
var body = jQuery('body');
// :: note that we're passing in a raw HTML element, not a jQuery-wrapped one
//    so you could also use document.getElementxxx() functions.
if ( $._data( body[0], 'events' ).click.length ) {
  // ...
}
```

Or to check if we have any event handlers bound to `click` under a specific namespace:

```
// :: assuming 
var body = jQuery('body');
body.on('click.foo', handler);

// ...

// :: using lodash
if ( _.any( $._data( body[0], 'events' ).click, { namespace : 'foo' } ) {
  // ...
}
```
