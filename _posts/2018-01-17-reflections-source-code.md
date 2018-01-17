---
title: "Reflections on reading source code"
layout: post
categories: posts
published: true
---

It has been a few months since I started digging into source code, and what a fun and fulfilling journey it has been. Here are some of the things I have learnt.

#### 1. Don't stop building stuff
At the start of this journey, I spent a lot of time reading the documentation and source for Mithril. I got a new job last September and started looking into Vue's source because that is what I was using at work. However, in all of this, I forgot to continue building applications and trying new things. Whilst learning through reading the source is invaluable, unless you are putting what you learn into practice, it does not help. I left the new place towards the end of December (after 18 of us were made redundant) and I have used that time to start creating stuff again. I have been playing around with Vue, React, Mithril as well as Tachyons.css and Chart.js, and I can definitely see improvements in how I think about code. I made a number of basic applications to get the juices flowing again and during that process I stumbled upon a project which I can really sink my teeth into.

#### 2. Source code is not scary as you think
I used to be intimidated by the complex architecture and structure of the libraries and frameworks I use everyday. That changed when I stopped regarding them as incomprehensible black boxes and began thinking of them as learning opportunities. Mithril's small codebase helped in this because it made that first deep dive more accessible. After starting, I soon realised that whilst the people behind these tools were incredibly clever and talented, they were simply making better use of the same data structures and language features I had the same access to. 

#### 3. It's easier to read source code when you use the tool on a daily basis
This one should be obvious but it's easy to jump into the nearest library you see and subject it to intense interrogation. When I did a <a href="{{ site.baseurl }}/posts/under-hood-express">brief </a> overview of the modules used by express.js, I found I did not retain what I learnt as much or had the same drive as with Mithril's code, for example, because I was working without a context. My experience with express has not been in a commercial environment, so it did not reap the same benefits as when it **properly** sunk in that in Mithril, virtual DOM nodes are simply JavaScript objects, for instance. 

#### 4. Reading source code teaches or makes you aware of new things
What specifically do I know now that I did not know before? 
- [FLIP animations](https://aerotwist.com/blog/flip-your-animations/) 
- How to use Chrome dev tools to step through your code and inspect the values at that moment in the program
- Using the `||` operator to set default values (although you can now use default parameter values for that in ES6)
- There is a [library](https://github.com/lightsofapollo/json-templater) for doing mustache style template replacements on JavaScript and JSON objects
- Mithril's entry point is a function which returns an object whilst Vue's entry point creates an object through calling `new` on a function
- In Vue, you can use a custom validator function to validate the props a component recieves. See [this](https://vuejs.org/v2/guide/components.html#Prop-Validation) for more
- You can use document fragments to append elements to the DOM without causing reflows or repainting
- You can use the `TypeError` object to create more specific error messages
- A lot of the express.js dependencies are modules which deal with HTTP headers
- According to the ECMAScript spec, there is no difference between `throw Error` and `throw new Error`
- `!==` checks if the variables given hold the same **value** and are the same **type**, whilst `!=` only checks if the variables given hold the same **values**