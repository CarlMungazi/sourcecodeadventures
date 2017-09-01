---
title: "Understanding Mithril's hyperscript function"
layout: post
categories: posts
published: true
---

*Earlier this year at work we re-wrote an internal framework we used to create SPA e-learning courses. After briefly trying out React, Angular 2, Ember and Vue, we settled on [Mithril](https://mithril.js.org). If I were to compare it to the frameworks we tried out, I would say it's more like React but with a simpler, smaller codebase. By the way, if you like geeking out on code articles, the articles from Mithril's old site have some real nuggets of [gold](http://lhorie.github.io/mithril-blog/). A few months after the re-write was done, I dug into Mithril's codebase to gain a deeper understanding and this is what I found...*

---

The main entry point into Mithril's source code is the `m()` function, which is a hyperscript function that, according to the [docs](https://mithril.js.org/hyperscript.html), represents an element in a Mithril view. It's demonstrated below as:
```javascript
m("div", {id: "box"}, "hello")
// equivalent HTML:
// <div id="box">hello</div>
```

And the full hyperscript function is below (as of July 24, 2017):
```javascript
function hyperscript(selector) {
	// Because sloppy mode sucks
	var attrs = arguments[1], start = 2, children
	if (selector == null || typeof selector !== "string" && typeof selector !== "function" && typeof selector.view !== "function") {
		throw Error("The selector must be either a string or a component.");
	}
	if (typeof selector === "string") {
		var cached = selectorCache[selector] || compileSelector(selector)
	}
	if (attrs == null) {
		attrs = {}
	} else if (typeof attrs !== "object" || attrs.tag != null || Array.isArray(attrs)) {
		attrs = {}
		start = 1
	}
	if (arguments.length === start + 1) {
		children = arguments[start]
		if (!Array.isArray(children)) children = [children]
	} else {
		children = []
		while (start < arguments.length) children.push(arguments[start++])
	}
	var normalized = Vnode.normalizeChildren(children)
	if (typeof selector === "string") {
		return execSelector(cached, attrs, normalized)
	} else {
		return Vnode(selector, attrs.key, attrs, normalized)
	}
}
```

The function takes three arguments: 
- a string (css selector) or object (Mithril component) 
- attributes object (html attributes or element properties)
- an array (Mithril vnodes), string, number or boolean

The first part of the function is as follows:
```javascript
function hyperscript(selector) {
  // Because sloppy mode sucks
  var attrs = arguments[1], 
  start = 2, 
  children

  if (selector == null || typeof selector !== "string" && typeof selector !== "function" && typeof selector.view !== "function") {
    throw Error("The selector must be either a string or a component.");
}
```
The variable `attrs` holds the attributes object (html attributes or element properties). It appears `start` will be used to check for the third argument later and then this will be assigned to `children`.

Before anything happens you always want to check the arguments given are what the function expects. The if statement is asking the following questions:
- Is the first argument equal to the `null` value? If this returns `true`, then throw the error BUT...
- If the first argument returns `false`, then return the result of the second expression, which is asking...
	- Is selector a `string` or `function`? If the `typeof` operation returns `true`, throw the error.

If we have been given what we expected, let us continue...
```javascript
if (typeof selector === "string") {
    var cached = selectorCache[selector] || compileSelector(selector)
}
```
If we're given a string, the following happens:
- We check if the selector (html element) we've been given exists on the `selectorCache` object (defined elsewhere in the code) which holds all the selectors already used. 
- If it doesn't, the `compileSelector` function (defined elsewhere in the code) adds the selector to the `selectorCache` object.

```javascript
if (attrs == null) {
    attrs = {}
  } else if (typeof attrs !== "object" || attrs.tag != null || Array.isArray(attrs)) {
    attrs = {}
    start = 1
}
```
The code above creates an empty attributes object in the event that an attributes object is not passed as the second argument in the first place. As a side note: `!==` checks if the variables given hold the same **value** and same **type**, whilst `!=` only checks if the variables given hold the same **values**. Also notice that `start` is assigned the value `1` if the attributes object does not exist.

```javascript
if (arguments.length === start + 1) {
    children = arguments[start]
    if (!Array.isArray(children)) 
      children = [children]
  } else {
    children = []
    while (start < arguments.length) 
      children.push(arguments[start++])
}
```
This if statement is doing the following:
- If we have been given three arguments, we assign the third argument (which can be an array (Mithril vnodes), string, number or boolean) to the `children` variable. And if this third argument is not an array, we turn it into one.
- If we have not been given three arguments we assign an empty array to `children`. The while statement only runs in the event we have splat arguments (if the argument is an array, you can omit the square brackets and have a variable number of arguments in the method instead). A splat argument in the context of this function means that `m("div", {id: "foo"}, ["a", "b", "c"])` can also be written as `m("div", {id: "foo"}, "a", "b", "c")`, which is essentially what we do in Mithril a lot. At the end of this, `children` holds an array of the arguments passed into the function.

```javascript
var normalized = Vnode.normalizeChildren(children)
```
The code above does the following:
- The `Vnode.normalizeChildren` function executes the `Vnode.normalize` function on every element in the `children` array and then returns that array
- The new array is assigned to the `normalized` variable

```javascript
if (typeof selector === "string") {
	return execSelector(cached, attrs, normalized)
} else {
	return Vnode(selector, attrs.key, attrs, normalized)
}
```
The last part of the hyperscript function does the following:
- If the `selector` argument passed into the function is a string
	- execute the `execSelector` function with the given arguments 
- If the `selector` argument passed into the function is not a string
	- execute the `Vnode` function with the given arguments

### Reflections:
1. Reading soure code is **incredibly** difficult BUT rewarding. I still have much to learn but being able to dive into the guts of a framework you use frequently is empowering
2. Reading source code exposes your blind spots and gives you more material to add to your self-study curriculum. Some of the things I didn't fully understand that I plan to look up include:
	- Splat arguments
	- The difference between `throw Error` and `throw new Error` (Edit: According to the [JS spec](http://www.ecma-international.org/ecma-262/7.0/#sec-error-constructor), there is no difference) 
3. When reading source code the first time round, worry more about the *what* instead of the *why*. When I came to the `normalized` variable assignment I ended up doing a bit of a deep dive into the `Vnode.normalizeChildren` and `Vnode.normalize` functions by spinning up some example code and logging the results of different statements. I should have simply stated what those functions did and moved on because it was clear enough.
4. What I learnt that I did not know before: 
	- `!==` checks if the variables given hold the same **value** and are the same **type**, whilst `!=` only checks if the variables given hold the same **values** (comparing reference values adds some further subtlety which I hope to tackle some other time. H/t to [@pakx](https://github.com/pakx) for pointing this out in the Mithril [gitter chat](https://gitter.im/mithriljs/mithril.js)) 
	- The source of this error message: `"The selector must be either a string or a component."`