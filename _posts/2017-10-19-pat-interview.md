---
title: "How I Use Mithril: Pat Cavit"
layout: post
categories: posts
published: true
---

*How I Use Mithril is an interview series in which developers talk about how they use Mithril.js and how they structure their development workflow*

---

### Q. What made you choose Mithril and how are you using it? 

I had been using YUI3 at [ArenaNet](https://arena.net) for years, and Yahoo! had just announced that they were ending support for YUI. This wasn't a surprise, but the official notification of it still hurt. We had a lot of YUI code/infrastructure, I'd given talks at YUIConf; we had been all in. It wasn't going to stop working the next day or anything but we decided that any new projects would need to be built on a new library. React had been announced not too long previously and I had made a snap decision that I didn't much care for it based on JSX and their lifecycle names. I never claimed I made great decisions.

In fairness, the size of React and the overall speed were also pretty off-putting. I was already used to modular bundles at runtime from YUI and was pretty determined to find something small to use as our view layer. The ideas behind React seemed worthy of investigation, so I began investigating this "VDOM" paradigm and it sounded pretty interesting. I was familiar with Immediate-Mode GUIs from working at a gaming studio so the idea of writing view code that worked like that was appealing. Within the limits of using the DOM we weren't going to get true Immediate-Mode rendering, but VDOM seemed like a smart tradeoff. Finding a small, performant VDOM library wasn't something I had much luck with though.

As it happens, I had just recently subscribed to the [npm addict](https://npmaddict.com) feed on a whim and I first saw Mithril go past there in early 2014. It just happened to scroll past one day and due to my pre-existing interest in alternative VDOM implementations I opened the repo and poked around.

I noticed some things pretty quickly:
1. Leo (Mithril's author) and I have very different personal coding rules
2. Mithril's simple API/lifecycle and batteries-included design were appealing
3. Auto-redraw sounded powerful but I had concerns about performance
4. The hyperscript API looked a little nutty, but at least it was recognizable as JavaScript
5. The docs were really good

I asked one of the experienced developers on our team to build a small experimental project with it and he came back a few days later with a relatively glowing review. I spent some time experimenting with performance in a small stress test project and determined that lo and behold, auto-redraw really did work fast enough for our uses and it provided tons of simplification for our codebase.

ArenaNet ended up using Mithril for a wide swath of things, it became our main DOM interaction layer. The ArenaNet website, the [Guild Wars 2](https://www.guildwars2.com/) website, and multiple elements within Guild Wars 2 itself. The game launcher, the real-money store, the currency exchange, the trading post, and all of the Guild Hall management UI. Anything that was done with web tech we wrote in Mithril.

I recently began working at Amazon Game Studios on Crucible and am using Mithril for multiple parts of the game UI, with plans to continue using it whenever possible.

### Q. How do you structure your Mithril apps?

I tend to have an `index.js` at the root that handles `m.mount()`/`m.route()`, then I break out each individual page as well as a layout component to use with a [RouteResolver](https://mithril.js.org/route.html#routeresolver), so I get diffing between pages. Each page will live in its own directory with any components and assets it uses, with a top level "components" directory for anything that's shared across pages.

I've tried out a bunch of different state management libraries and found them all to be pretty unwieldy. I usually end up creating a top-level `state.js` file that contains a POJO for state, along with some utility functions for doing complicated multi-step state alterations. I'm usually working on something with, at most, one other programmer, so being able to scale up to a big team using something like Redux or Mobx isn't terribly appealing.

[Crucible](https://github.com/tivac/crucible) is a now slightly out of date example of how I build most of my Mithril apps. It's not a particularily complex or interesting structure but it seems simple and straightforward, which I value quite a bit.
 
### Q. What other frameworks have you used? How do they compare to Mithril?

I've been deep into YUI3, dabbled with Backbone and React, read the Ember docs, and tried to get Vue running once only to throw up my hands in frustration. Lately, I'm looking closely at [Svelte](https://svelte.technology/) because of the performance critical work I'm doing within game engines.

Backbone was a great step forward in terms of structuring an application in a MVC-ish way. Unfortunately, MVC is actually a tricky concept to apply to a web application and I don't think it really works all that way in the long term.

YUI3 was a powerful library that bundled a bunch of useful widgets. I miss it dearly. What I miss less is getting into custom event hell where you've got a spaghetti nightmare of events triggering events and determining what is actually going on became almost impossible. I still haven't run across another library with a DOM event normalization/customization layer that was half as good as YUI's, though.

React's approach with VDOM was revelatory. I wish it was smaller, faster, and I liked their APIs better. Ultimately, those issues led me down the path towards finding Mithril, so I can't be too upset about it.

Svelte is a really interesting approach. Instead of doing any sort of VDOM diffing it takes your templates and generates raw DOM calls in JavaScript. Essentially, the framework disappears and it's just your application code transformed into the bare minimum needed to accomplish your goals. It's still pretty new and a little rough around the edges but I think it shows a lot of promise. It's also blazingly fast since it can track individual text nodes and update just the one single tiny piece of DOM necessary without comparing two large tree structures in memory.

### Q. How do you structure your development workflow? What kind of tools do you use?

I'm a rollup fan, mostly due to the simple API and clean bundles it outputs. It's a silly reason to choose a tool but when I'm interacting with a tool or the output of a tool why shouldn't it be nice to look at? Webpack is considerably more powerful in most ways and is catching up quickly to rollup on the code-shaking front, but given my experience maintaining a webpack plugin I really don't love using that tool.

I wrote [modular-css](https://github.com/tivac/modular-css) because I thought the idea behind CSS Modules was brilliant but the implementation was just broken on Windows. I've since diverged a bit from the CSS Modules spec in adding new features that I found useful, but it's definitely my preferred approach for CSS these days. I've tried multiple CSS-in-JS libraries to make sure I'm not missing anything and I find all of them awkward to author in. I'll stick with my `.css` files for now and my tooling that prevents me from blowing my own styling foot off.

Since I'm using a PostCSS-powered workflow with modular-css, I tend to use multiple PostCSS plugins to accomplish various nice things. [postcss-nested](https://github.com/postcss/postcss-nested), [postcss-calc](https://github.com/postcss/postcss-calc), and [autoprefixer](https://github.com/postcss/autoprefixer) all come to mind as very useful tools that save me a bunch of time and energy. Oh, [CSS MQPacker](https://github.com/hail2u/node-css-mqpacker) is also excellent and something I really enjoyed using while working on [ArenaNet](https://www.arena.net/)!

### Q. What do you enjoy the most about using Mithril?

The simple, straightforward API. The predictable redraws. Not worrying about manually redrawing most of the time. Mithril has a lot of really pragmatic choices baked-in which I find incredibly valuable every single day I program in it.

Also, `m.request()` and `m.route()` mean I generally don't have to go evaluate another tool or figure out which fetch polyfill sucks the least. I can just get on with my work and have tools that are often good and at the very least "good enough" to accomplish what I want without a bunch of research.

### Q. And what do you enjoy the least?

Leo's code style :grinning:.

My real answer is that I wish I understood the diffing algorithm and some of the other core systems better. They're complicated because they're doing a complicated thing, but I don't love not fully understanding the nuts and bolts there. I keep trying to set aside time to really work my way through it but life finds a way to keep me busy all the time.

### Q. What would you say is the best way for a beginner to learn using Mithril?

Check out the site, ask questions in Gitter, and play around! Having a small task in mind will help as aimless programming isn't generally very productive. Fortunately, Mithril is excellent for prototyping out small ideas because of the aforementioned batteries-included model.

I honestly can't recommend Mithril's Gitter room strongly enough to both beginners and experienced programmers using Mithril. Some of the most insightful programming conversations around being had by genuinely wonderful people.

And sometimes I chat in there too :grinning:.