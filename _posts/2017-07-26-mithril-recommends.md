---
title: "Recommendations from mithril's docs"
layout: post
categories: posts
published: true
---

Before reading a library or framework's source code, it's advisable to go through the documentation. Thankfully, mithril's size means reading through the main APIs is not that arduous a task. When reading the docs, you will often find notes from the author(s) recommending different ways of using the available features. This is useful when you read the source because you can more easily understand *why* certain bits of code exist. I've noted below five recommendations I came across during my latest round of reading the mithril docs. I found more than five but I'll go through the rest at a later time. The example code below each recommendation has been taken from the docs.

- **Use CSS selectors for attributes with unchanging values and pass an attributes object for attributes with values that can change**

```javascript
m("div#hello") // static

m("a.link[href=/]", { 
	class: currentURL === "/" ? "selected" : ""
}, "Home") // dynamic
``` 

- **Got a list of data? Use an array to iterate over it**

```javascript
var users = [
	{name: "John"},
	{name: "Mary"},
]

m("ul", users.map(function(u) { // <ul>
	return m("li", u.name)  //   <li>John</li>
				//   <li>Mary</li>
})) 
```

- **Conditionally set the content in a mithril view by using a ternary operator**

```javascript
var isError = false

m("div", isError ? "An error occurred" : "Saved") // <div>Saved</div>
```

- **Unmount a component on an element by using `m.mount(element, null)`. This also cleans up Mithril's internal state. The code which does that can be found [here](https://github.com/MithrilJS/mithril.js/blob/8ab31790abdb391a2daba5a721b786302904a172/api/mount.js). If you're feeling lazy, the function is below**

```javascript
function(redrawService) {
	return function(root, component) {
		if (component === null) {
			redrawService.render(root, [])
			redrawService.unsubscribe(root)
			return
		}
		
		if (component.view == null && typeof component !== "function") throw new Error("m.mount(element, component) expects a component, not a vnode")
		
		var run = function() {
			redrawService.render(root, Vnode(component))
		}
		redrawService.subscribe(root, run)
		run()
	}
}
```

- **When using `m.route`, improve the user's navigation experience by taking advantange of the [`history.pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History_API) API to 'remember' the state of things such as large forms. If the user nagivates away for whatever reason, the form is filled in when they return to that page. Example in the mithril docs can be found [here](https://mithril.js.org/route.html#history-state) and below.**

```javascript
var state = {
    term: "",
    search: function() {
        // save the state for this route
        // this is equivalent to `history.replaceState({term: state.term}, null, location.href)`
        m.route.set(m.route.get(), null, {replace: true, state: {term: state.term}})

        // navigate away
        location.href = "https://google.com/?q=" + state.term
    }
}

var Form = {
    oninit: function(vnode) {
        state.term = vnode.attrs.term || "" // populated from the `history.state` property if the user presses the back button
    },
    view: function() {
        return m("form", [
            m("input[placeholder='Search']", {oninput: m.withAttr("value", function(v) {state.term = v}), value: state.term}),
            m("button", {onclick: state.search}, "Search")
        ])
    }
}

m.route(document.body, "/", {
    "/": Form,
})
```