---
title: "How I Use Mithril: Barney Carroll"
layout: post
categories: posts
published: true
---

*How I Use Mithril is an interview series in which developers talk about how they use Mithril.js and how they structure their development workflow*

---

### Q. What made you choose Mithril and how are you using it? 

Just over 3 years ago I joined a small startup as interface designer & developer for an application we would be making from scratch. Previously, I'd been working fixed-term contracts at agencies & small businesses and the choice of which 3rd party technologies I'd use (if I had a say at all) was always dependent on a notional idea of maintainability based on the familiarity of other engineers or received wisdom based on what seemed to be popular. 

This was the first time I got to call the shots and I decided Mithril was the right call. Mithril didn't present itself as an exotic solution to old problems like most other frameworks did: it was simply a clearer, more efficient and more imaginative interface to problems that had broadly already been solved. It did everything React did with far less ceremony, and it was 'batteries-included' – the main sell was a good functional hyperscript API to make DOM structures, but it also included simple but complete mechanisms for AJAX and state-keeping. 

Rather than selling itself with "here's an easier alternative way to do things you previously struggled with", Mithril was at once more modest and more ambitious – the blog, especially, was interested in powerful new abstractions instead of "how to deal with common problem X". That inspired me. Unlike its competitors at the time, it encouraged writing in pure Javascript and had a very minimal interface with the DOM, so I was confident that my choice of a less-than-common library wouldn't be too much of a barrier to entry for anybody else who needed to debug or extend the code I was writing.

At the time I was using it to build a complex dashboard application for an Internet of Things platform. I would still definitely use Mithril for any web application I needed to write. But even for simple exercises involving stateful DOM interfaces, I will typically reach for Mithril – even if I'm ultimately required to implement the solution in another framework. It's always far more simple and powerful.

### Q. How do you structure your Mithril apps?

Every app starts with `npm init`. NPM is essential for managing dependencies. Even if you don't want to touch Node, unpkg.com works as a CDN for NPM. The principle gain is that every app has a manifest file with a README describing how to work with it and where and how to get the bits and pieces that make the raw code a viable product. I used to use Browserify to compile modular Javascript files into production-ready Javascript code, but these days I prefer Rollup.

Any web project ultimately consists of an index.html with a script element that calls index.js and the rest is pure Javascript. I try to keep everything in that index.js until things get too complicated and I need to break out some functionality to another module – because it's very involved in some very specific aspects (if I need to do something like drag-and-drop for UI widgets), or because the file I'm writing is just getting too long (200 lines of code or more) that any single file / module is getting too difficult to think about as a whole. I never, ever, separate things into discrete structures in anticipation of future difficulties. I find that when I do that I end up saddled with premature ideas of problems I might have later, which end up compromising my thinking later on when complexity actually does emerge.

Other than that, pretty much everything is a component. I only break things out into discrete utility modules as and when repeated problems emerge and I figure out a consistent way of dealing with them that makes the bigger picture easier to reason about. In my opinion, you can only really justify that when you've found the difficulty of keeping everything in one place the hard way.
 
### Q. What other frameworks have you used? How do they compare to Mithril?

In historical order: Backbone, Ember, Mithril, Ember again & React. I've had a hand in Angular and ExtJS but they're too bad to be worth talking about.

Backbone was great for my personal learning because it was the first framework to offer powerful organising concepts that encourage systematic modularisation without dictating the shape and content of those types, but ultimately it was very hard work to develop complex systems out of it. Ember was the opposite, it makes loads of simple, mundane stuff (how do I deal with HTTP, forms, lists?) totally easy, but in so doing introduces a huge amount of one-size-fits-all magic that makes genuinely complex requirements *incredibly* complicated. If you don't get Web development, or you do get it but you hate it, Ember makes some kind of sense. But if you are ultimately willing or required to engage with complex requirements in the underlying languages of the Web, you end up spending all your ingenuity fighting it. Really not for me.

React is OK. The component interface is flexible enough to allow most things, and the JSX syntax and Facebook marketing make it appealing and popular enough to justify using it.

I would like to play with hyperHTML — it sticks to that thing most successful UI frameworks do (writing views as if they were HTML templates) — but does it more modestly and elegantly. It's also a lot more savvy about the real challenges of DOM mutation than any other framework out there.

The 2 things I resent most about the vast majority of modern Web frameworks is their superficial insistence that writing something that looks a lot like HTML is more valuable than getting flexible control over the DOM (which is ultimately what they're all about when it comes to views) and the baggage of convoluted types they force you to learn about in order to structure an application. Most of these don't stand up: they assert that model management is too complicated, and offer exotic patterns to deal with simple requirements, but end up creating their own breed of problems when things get complicated. In a sense, most frameworks are based on the premise that the bare basics of web programming are too complicated for their target audience, but leave you high and dry when you want to do something that actually is difficult to think about. Worse, you end up thinking about how you can try and make things work *despite* the framework. I really hate that. Mithril doesn't do that. Mithril is the most modest yet ambitious framework I've ever encountered in that it makes no pretence at dealing with 'outside context problems' but in presenting a non-patronising simple API allows you to think about the genuinely tough problems better.

### Q. How do you structure your development workflow? What kind of tools do you use?

Everything starts with a simple one-file sandbox - I used to use JSbin, now I use Flems.io. This way I don't have to think of a name, or create any files or folders that I'll probably just have to rethink and delete later. When I've got a clear enough idea of how things are going to break out into a multi-part package, I create a repository on Github, clone it to my file system, and create an NPM package using Yarn. I install Rollup & Babel and a bunch of plugins such that I can write code in my favourite text editor (Sublime) and have it trace all my dependencies and compile into a single Javascript file which is loaded by a central index.html. I start a server at that location to simulate Web usage and get coding by trial and error, breaking things out when they get more complex.

The single greatest asset in all of this is Chrome's developer tools. I will often write a function knowing that it needs to do something but not knowing how to do that thing. Using the browser dev tools I put a breakpoint in that function and then stop to observe what runtime references are available, what the current state of the environment is, and stop to think about the best way of doing things. I'm shocked by how many developers I meet who define loads of variables, write classes etc, and only *then* see what happens. I always write the simplest code that doesn't work *first*, then execute it to see what my options are - it's about giving yourself the opportunity for insight at the fork of the road rather than believing that you should or can determine ahead of time the best fork to take before encountering it.

I'm seriously amazed how poorly utilised browser developer tools are used. Back in the day, 'real' programmers used to scoff at web developers because they didn't have Integrated Development Environments - these days, we have the very best IDEs. Next time you're developing or bugfixing and your app does something unexpected, use breakpoints and try and work out where things might be going squewiff - Chrome allows you HTTP and DOM breakpoints, so you don't even have to guess about what part of the code is causing the problem - and invest in observing what's happening in your runtime *at the time* that it's happening. It's illuminating.

### Q. What do you enjoy the most about using Mithril?

Mithril doesn't try to solve problems it doesn't know it can solve. Every other framework I've encountered has at its root some strong idea(s) that the authors have figured their way out of a tricky problem and they have a better way of thinking about that problem. Mithril stands on the shoulders of giants - Snabbdom & jQuery, and recently, React - but it always makes *writing code* easier, without the pretence that it's solving mundane problems that its users are deemed too stupid or lazy to deal with.

There are imperfections & contentious issues, as there always will be: how do I structure and associate my models and communicate interactions? Why is HTTP so difficult sometimes? Why is the difference between DOM properties and attributes so bizarre sometimes? Mithril makes good calls on these, but doesn't claim to magic away the difficult issues where, depending on your specific circumstances, things are complicated in a certain way that nobody can be expected to have a 'right' solution. 

Whereas most frameworks are optimistic about their authors intellect and dismissive of their users ability, Mithril encourages its users to be optimistic about their own ability, and admits that only they will ultimately know how to deal with things best.

Last but not least, the Mithril community is incredible. Never have I met such an engaged, diverse & supportive crowd of people. As with all communities, you will end up occasionally facing a recurring character that is ultimately unsympathetic. But given time you will find incredibly supportive people who will elevate you in pragmatic understanding and philosophy and sheer love. It sounds silly but it's true - Mithril attracts good people and doesn't encourage bad people.

### Q. And what do you enjoy the least?

Mithril is a double-edged sword when it comes to the mantra of "don't make me think". When all other frameworks make a call about how their users can't or shouldn't have to think about things, and offer a guided path, Mithril leaves the road open. That can be incredibly frustrating. *I* find that liberating, but I can see how a lot of people find that obnoxious. The truth is most developers are expected to do perfunctory jobs in function of the tools and environment provided to them. I've been incredibly lucky, middle class in a sense most people will never have — I can and have failed at loads of jobs and was always able to think "what do I want next" and been able to drop out when things got tough and come back in to the industry without question. 

The possibility space of 'programming' is so massive that developers are expected to be able to envision and develop entire economies by themselves. Everybody wants to believe that but it's a total exception. The story about the developer who changed the world and had the opportunity to do it is a statistical oddity. And I realise, to some degree, that I benefit from huge privilege in this space. I can afford to make and own mistakes and still be comfortable in ways 99% of developers don't have. So in a way your programming framework is your union. If you're not respected or your job is precarious, you can't afford to say "this is tricky but I'll figure it out my own way". I've always been able to do that. I've been fired, and I get hired again.

When that's not the case - as it is for most people - the commonly accepted restrictions of any given framework are a safety net. People hire & judge you on the basis of a higher order evaluation of the intrinsic worth of React, and you can point to external evidence that x, y or z is a tricky problem with React. With Mithril you have very few of those defenses. Mithril is niche, and if you get called upon to justify your decisions in your job you have to rely on a trusting, rational and / or open-minded employer or boss. That's not the norm.

As a result, I don't preach Mithril. Nobody preaches Mithril on the political level that funded frameworks evangelise themselves. On an economic & political level, practically, you need a rare degree of freedom to be able to choose Mithril above a more popular, corporate-backed framework.

If anybody chooses to engage with Mithril I will do my utmost to help them, in my belief that it's fantastic. But I don't and won't evangelise it.

### Q. What would you say is the best way for a beginner to learn using Mithril?

Everything you write should be a component. What about state management? What about immutability? What about best practice X that doesn't seem to be addressed? Doesn't matter. You're in control. Some of these things are tough but you have to figure them out yourself, and until you've experienced that lesson on a case by case basis, according to your particular struggle, you won't solve it in a way that makes sense in context. There is no generic solution, there's only the solution that works for you.

Don't be afraid of repeating yourself. Don't be afraid to ask a question because you feel that that question is stupid in some way. Join Mithril Gitter, chat shit. Think you're uncouth? Think you're way out of your depth? Think you're irrelevant to the wider picture? Join the chat. Do it under a pseudonym. You will find support.