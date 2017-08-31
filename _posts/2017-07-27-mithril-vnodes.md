---
title: "Mithril vnodes"
layout: post
categories: posts
published: true
---

In my first explainer of Mithril's source code, I broke down how the [hyperscript](https://gist.github.com/CarlMungazi/76d5c384506cebba0afec41985b9905d) function worked. It's main task is the creation of virtual DOM nodes (vnodes), the objects which represent DOM elements or parts of the DOM. So how are these objects created? 

### Vnode functions
This is the function which creates a vnode:
```javascript
function Vnode(tag, key, attrs, children, text, dom) {
	return {tag: tag, key: key, attrs: attrs, children: children, text: text, dom: dom, domSize: undefined, state: undefined, _state: undefined, events: undefined, instance: undefined, skip: false}
}
```
This function is assisted in its work by these two helper functions:
```javascript
Vnode.normalize = function(node) {
	if (Array.isArray(node)) 
		return Vnode("[", undefined, undefined, Vnode.normalizeChildren(node), undefined, undefined)
	if (node != null && typeof node !== "object") 
		return Vnode("#", undefined, undefined, node === false ? "" : node, undefined, undefined)
	return node
}
Vnode.normalizeChildren = function normalizeChildren(children) {
	for (var i = 0; i < children.length; i++) {
		children[i] = Vnode.normalize(children[i])
	}
	return children
}
```
The type of vnode we create is determined by the value of the `tag` property. The docs [state](https://mithril.js.org/vnodes.html#vnode-types) that a vnode can either be:
- an element
- a fragment
- text
- trusted HTML
- a component

### Element vnode
Let's go through *what* happens when we create each of these types. Suppose I write the following code: `m('div', {class:"foo"}, "Foo")`. The following vnode is created:
```javascript
{
	attrs: Object,
	children: undefined,
	dom: undefined,
	domSize: undefined,
	events: undefined,
	instance: undefined,
	key: undefined,
	skip: false,
	state: undefined,
	tag: "div",
	text: "Foo",
	_state: undefined,
	__proto__: Object
}
```
Because I've given `m()` a string as my first argument, one of the things it does is perform this conditional check:
```javascript
if (typeof selector === "string") {
	var cached = selectorCache[selector] || compileSelector(selector)
}
```
`selectorCache` holds an empty object, so when I use `'div'` for the first time, `selectorCache[selector]` returns `undefined`. And since `undefined` is one of JavaScript's six falsy values, `cached` is assigned the value of `compileSelector(selector)`. Note: the logical operator `||` does not necessarily return a boolean. According to the [JavaScript spec](https://tc39.github.io/ecma262/#prod-LogicalORExpression), "*The value produced by a && or || operator is not necessarily of type Boolean. The value produced will always be the value of one of the two operand expressions.*". It is also worth noting that because `compileSelector` returns the following assignment `selectorCache[selector] = {tag: tag, attrs: attrs}`, `selectorCache[selector]` is no longer `undefined`. In this instance, it returns the following object:
```javascript
{
	attrs: Object,
	tag: "div",
	__proto__: Object
}
```
When `m()` creates the vnode, the value of the `tag` property is assigned in the `execSelector` function and we end up with this vnode, which represents the `div` DOM element:
```javascript
{
	attrs: {
		class: undefined,
		className: "foo",
		__proto__ :Object
	},
	children: undefined,
	dom: undefined,
	domSize: undefined,
	events: undefined,
	instance: undefined,
	key: undefined,
	skip: false,
	state: undefined,
	tag: "div",
	text: "Foo",
	_state: undefined,
	__proto__ :Object
}
```

### Text vnode
What happens if I write the following code: `m('div', {class:"foo"}, "Foo", "Baz", "Wom", "Bat)`? This will still create a vnode for the `div` DOM element but instead of the `children` property being `undefined`, it will be an array of **four** objects, one for each string I have given as the last four arguments:
```javascript
{
	attrs: {
		class: undefined,
		className: "foo",
		__proto__ :Object
	},
	children: Array(4),
	dom: undefined,
	domSize: undefined,
	events: undefined,
	instance: undefined,
	key: undefined,
	skip: false,
	state: undefined,
	tag: "div",
	text: "Foo",
	_state: undefined,
	__proto__ :Object
}
```
The `Vnode.normalize` function does this through this `return` statement: `return Vnode("#", undefined, undefined, node === false ? "" : node, undefined, undefined)`. The `#` string signifies that the vnode being created is a text vnode.

### Trusted HTML vnode
So far we've touched upon `element` and `text` vnodes. Next we'll look at vnodes for `trusted HTML`. Mithril escapes all values by default to prevent cross-site scripting attacks, so the following code: `m('div', "<h1>Here's some <em>HTML</em></h1>")` will be rendered as a `div` with the text `<h1>Here's some <em>HTML</em></h1>`. The vnode looks like:
```javascript
{
	attrs: undefined,
	children: undefined,
	dom: undefined,
	domSize: undefined,
	events: undefined,
	instance: undefined,
	key: undefined,
	skip: false,
	state: undefined,
	tag: "div",
	text: "<h1>Here's some <em>HTML</em></h1>",
	_state: undefined,
	__proto__ :Object
}
```
But if we write `m('div', m.trust("<h1>Here's some <em>HTML</em></h1>"))`, we get what we have asked for and the vnode looks like:
```javascript
{
	attrs: undefined,
	children: Array(1), // the object in this array is below
	dom: undefined,
	domSize: undefined,
	events: undefined,
	instance: undefined,
	key: undefined,
	skip: false,
	state: undefined,
	tag: "div",
	text: undefined,
	_state: undefined,
	__proto__ :Object
}

// Our string, which has created a h1 element, is now a child of the div element
{
	attrs: undefined,
	children: "<h1>Here's some <em>HTML</em></h1>",
	dom: undefined,
	domSize: undefined,
	events: undefined,
	instance: undefined,
	key: undefined,
	skip: false,
	state: undefined,
	tag: "<",
	text: undefined,
	_state: undefined,
	__proto__ :Object
}
```
The difference between this vnode and the earlier one is that the `tag` property has a different value. Also, the string has been turned into a vnode and is now a child of the `div` element. In the source, this is the `m.trust` declaration:
```javascript
function(html) {
	if (html == null) 
        html = ""
	return Vnode("<", undefined, undefined, html, undefined, undefined)
}
```
Why are there no error checks to make sure this function receives the `string` argument it deserves? That's taken care off by the rendering process. So if the desired argument is missing, you will definitely hear about it.

### Component vnode
The fourth type of vnode Mithril creates is a `component` vnode. A Mithril component is simply a JavaScript object that has a method called `view`. 
```javascript
var Comp = {
    view: function() {
        return m('div', 'I am a Component')
    }
}
m.render(document.body, m(Comp));
```
So far we've been passing strings as the first argument to `m()`. But because we have now given it an object, it will return this `Vnode(selector, attrs.key, attrs, normalized)` function call instead of `execSelector(cached, attrs, normalized)`, and we end up with this vnode:
```javascript
{
	attrs: Object, // empty
	children: Array(0),
	dom: undefined,
	domSize: undefined,
	events: undefined,
	instance: undefined,
	key: undefined,
	skip: false,
	state: undefined,
	tag: {
		view: function(),
	        __proto__ :Object
    	},
	text: undefined,
	_state: undefined,
	__proto__ :Object
}
```

### Fragment vnode
Lastly, and definitely not least, we have fragment vnodes. Here is what the docs say about them: *Represents a list of DOM elements whose parent DOM element may also contain other elements that are not in the fragment. When using the m() helper function, fragment vnodes can only be created by nesting arrays into the children parameter of m(). m("[") does not create a valid vnode.* The docs for the `m.fragment` call (which also creates fragment vnodes but then also gives them an attributes object) has the following example of how you could construct fragment vnodes:
```javascript
var groupVisible = true

m("ul", [
    m("li", "child 1"),
    m("li", "child 2"),
    groupVisible ? [
        // a fragment containing two elements
        m("li", "child 3"),
        m("li", "child 4"),
    ] : null
])
```
Here we have an array of elements which contains a nested array containing further elements. What kind of vnode object does this create? 
```javascript
{
    attrs: undefined
    children: Array(3), // this contains our first two list elements and our nested array
    dom: undefined,
    domSize: undefined,
    events: undefined,
    instance: undefined,
    key: undefined,
    skip: false,
    state: undefined,
    tag: "ul",
    text: undefined,
    _state: undefined,
    __proto__ :Object
}
```
In the `children` array, the vnodes for the list elements look as you would expect. The nested array vnode object looks like:
```javascript
{
    attrs: undefined
    children: Array(2), // this contains our last two list elements
    dom: undefined,
    domSize: undefined,
    events: undefined,
    instance: undefined,
    key: undefined,
    skip: false,
    state: undefined,
    tag: "[",
    text: undefined,
    _state: undefined,
    __proto__ :Object
}
```
So how does `m()` create this data structure?

**1.** The fun begins with this conditional check:
```javascript
if (attrs == null) {
    attrs = {}
} else if (typeof attrs !== "object" || attrs.tag != null || Array.isArray(attrs)) {
    attrs = {}
    start = 1
}
```
- In this case, `attrs` holds an array of our list elements, so we skip the if statement and move on to the else if
- `attrs` is NOT an object and does NOT hold the `null` value but it IS an array so...
    - `attrs` is assigned an empty object
    - `start` is assigned the number 1 (when it was initialised it was given the number 2)

**2.** Then we move on to another conditional check:
```javascript
if (arguments.length === start + 1) {
    children = arguments[start]
    if (!Array.isArray(children)) children = [children]
} else {
    children = []
    while (start < arguments.length) children.push(arguments[start++])
}
```
- `arguments.length` is `2` and `start` + `1` = `2` so...
    - `children` is assigned an array of two objects and another array. How is this possible, I hear you ask? Is the second argument not an array of `m()` calls? That's what I thought but if you `console.log` the `arguments` variable or if you use your browser to debugger to place breakpoints in the `m()` function, you will find that Mithril first deals with the those `m()` calls (turns them into vnode objects). So by the time it comes to dealing with the initial `m('ul', [...])` call, it has a nice array to deal with instead of function calls
    - `children` now holds an array so this nested if statement returns `false`. In plain English, it is asking: "Is `children` NOT an array?"

**3.** The next step is this: `var normalized = Vnode.normalizeChildren(children)`. From our [earlier](https://gist.github.com/CarlMungazi/76d5c384506cebba0afec41985b9905d) deep dive into `m()`, we know that all this function does is apply the `Vnode.normalize` function on every element in our array. So this is what happens to each element:
    - `Vnode.normalize(node)` applies two conditional checks. If the `node` (the name given to the argument it expects) fails those checks, it is returned in the same state it was entered. The first check is a simple `Array.isArray(node)` check. The second check `node != null && typeof node !== "object"` returns `false` because the first expression returns `true` but the second expression returns `false`. Remember: the `&&` operator needs both expressions to be true for it to return true. The first two elements in `children` are returned as they are because but our third element, the array, is turned into a **fragment vnode** by this: `return Vnode("[", undefined, undefined, Vnode.normalizeChildren(node), undefined, undefined)`. So at the end of this, `normalized` now holds an array of three vnodes - two of which are `element vnodes` and the third a `fragment vnode`

**4.** The final step is: `return execSelector(cached, attrs, normalized)` and this returns the object we started with earlier.    

So, in summary, when using Mithril (and I supposed this also extends to other virtual dom frameworks), vnodes are among the most important data structures we work with because they represent the DOM elements painted on screen. Before I dug into the source I used to think of them in somewhat mystical terms but once I realised they were simply objects which happened to be doing a lot of cool stuff, I started approaching them with less trepidation. Also, this deep dive focused exclusively on the *type* of vnodes Mithril creates so when I revisit this topic I will look at the other properties available on vnodes.   

### Reflections
1. A deeper understanding of DOM creation would help me appreciate more how virtual dom works. Links for further study: [this](https://developer.mozilla.org/en-US/docs/Introduction_to_Layout_in_Mozilla) and [this](http://taligarsiel.com/Projects/howbrowserswork1.htm#Parsing_general)

2. I always assumed the `||` check returned a boolean. However, it can actually be used to set default values. Kyle Simpson's book You Don't Know JavaScript has a **LOT** more detail on this in these [two](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch4.md) [chapters](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch5.md)

3. Investigating fragment vnodes lad me to the discovery of the [DocumentFragment](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment) object. You can use this object to append elements to the DOM without causing reflows or repainting. Read [this](https://davidwalsh.name/documentfragment) blog post for more or checkout `document.createDocumentFragment`(https://developer.mozilla.org/en/docs/Web/API/Document/createDocumentFragment)

4. I need to study further the subtleties between `!=` and `!==` checks. It keeps cropping up and I need to get a better handle on it. 

5. Stepping through functions calls in the Chrome debugger can make your head hurt! But it's a really useful tool for checking the values and state of different data structures. Definitely a tool to add to the source code reading tool box.

6. Digging into the creation of fragment vnodes was by far the most time-consuming part of this exercise. And even though I feel my understanding is still fuzzy, that is a good thing because it means the ideas will only get clearer the more code reading I do

7. An extension of my study on vnodes could be creating my own virtual dom library as described in this [article](https://medium.com/@deathmood/how-to-write-your-own-virtual-dom-ee74acc13060)   