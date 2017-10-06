---
title: "How I Use Mithril: Rasmus Porsager"
layout: post
categories: posts
published: true
---

*How I Use Mithril is an interview series in which developers talk about how they use Mithril.js and how they structure their development workflow*

---

### Q. What made you choose Mithril and how are you using it? 

After using jQuery, Backbone, and Angular in several projects, I found myself looking for a better tool to build web apps. Every tool I'd used previously led me to a stage in the project where I felt I lost control. Either I had implemented too many weird hacks to achieve my goals, or the structure broke apart when the app reached a certain size.

I was certain a better way was out there, so over the course of a half year I went through all the major options available. This started mid-2014, just when React was gaining traction and Ember pushed hard to get attention. Many times I passed Leo's blog and the Mithril website while scouring the web, but unfortunately disregarded it due to the pale, old look it had. It wasn't after trying out React and Ember and being frustrated that I got caught up in Leo's blog posts. I was hooked. In less than a day I had built sample apps that I hadn't even managed to finish in React or Ember in a week. This was great! I wasn't sure Mithril could cover all my different cases, but for all those small kiosk web apps I had to build at the time, it was hard to ignore the efficiency.

Since then, I've put Mithril to use in about 20 kiosk/infoscreen apps, a desktop softphone & chat app, two admin/CMS sites, a small game and more recently, a web playground called [Flems](https://flems.io). Mithril changed my workflow completely. Now I suddenly didn't need to start my mockups in Photoshop anymore - going straight to coding seemed more natural and it was much faster.

### Q. How do you structure your Mithril apps?

Recently, I've stopped obsessing about app structure, specifically state management, and I'm instead just letting it appear naturally as I move along. I've always been trying to find the holy grail of state management in the belief I would be better able to tame the beast of feature creep. I've tried many different ways and every time I tried to follow a specific pattern, I ended up painting myself into a corner.

I haven't completely given up on finding a good state management pattern but I'm obsessing much less about it now. I think in often trying to keep things DRY, I've created wrong/limiting abstractions and unnecessary, specific couplings. I'm trying (slowly) to learn more functional programming concepts, which I'm sure will lead me to using very different structures. I hope I can find time to try out the [Meisos](http://meiosis.js.org) pattern soon.

I also made and use [BSS]( https://github.com/porsager/bss) to put styling directly on the hyperscript elements. This avoids the entire CSS class naming hurdle and keeps things that are inevitably connected close to each other. That's one thing I've come to enjoy - having everything for my components specified in a single file. These components usually grow in size as complexity increases but once they become too big (usually anything above 100 lines) it's trivial to cut out a block and stuff it in its own file as a separate component.
 
### Q. What other frameworks have you used? How do they compare to Mithril?

Of the frameworks comparable to Mithril, I've used Backbone with Marionette, Angular and dabbled in Ember and React. Backbone was the first real framework I've used and it involved a lot of boilerplate with tying up events and having views (templates) separated from component logic. Backbone wasn't hard to learn but in the long run it was just too much typing and switching between files to implement simple things.

Angular was basically the same but with even more abstractions and magic handlers, resulting in a really steep learning curve. Just when I thought I had figured it out I was thrown into a boxing match with a straitjacket on. I felt it was impossible to stay sane making anything more than a simple form in Angular.
Giving Ember a shot tasted too much like Angular, and giving away my freedom for "one true way" didn't appeal to me after that initial feeling.

Then there's React. I actually didn't understand React until after I used Mithril. I think that says a lot about Mithril. It is much simpler than any of these frameworks, has a much lighter learning curve, and does almost all that is needed in a small, performant package.

### Q. How do you structure your development workflow? What kind of tools do you use?

Getting started quickly and being able to iterate with instant feedback is very important for my workflow - maybe even to a fault. I have probably spent too much time making tools to improve my workflow compared to actual work, but hopefully the tools can be useful to other people as well.

For local development I use a tool I've made called [Wright](https://github.com/porsager/wright), which launches Chrome in dev mode and then hot reloads anything (HTML/CSS/JS) any time I change a file. On my standard projects I have a TTS (Time To Screen) of around a half second without losing the current app state. It also auto-reloads any other connected browser, so testing on multiple devices and browsers is fairly trivial.

For quick testing or mockups I would have a Wright playground project that I could get started fairly fast but sharing that code for review or feedback was quite annoying. I've since built flems.io (using Mithril, of course), which is a web playground with proper error feedback and logging. It saves everything in the url for easy sharing. It's such a delight being able to quickly test an assumption, so my first bookmark is currently [https://flems.io/mithril.bss](https://flems.io/mithril.bss) where I can test out things with my usual tools instantly.

### Q. What do you enjoy the most about using Mithril?

It makes me a better developer. It embraces JavaScript and gives me the tools I need to tame the DOM. It has a community of extremely clever, kind and welcoming people where I've learned so much of what I know today.

### Q. And what do you enjoy the least?

I can't really come up with anything, so I might have to think some more on this one.


### Q. What would you say is the best way for a beginner to learn using Mithril?

Get your hands dirty and leave old assumptions behind. You don't need classes and you don't need `this` (closures can replace those almost every time). If you have a small app previously written in another framework, try to re-write it with Mithril. Read the documentation on lifecycle hooks and join the [Gitter chat](https://gitter.im/mithriljs/mithril.js). People there are very welcoming and love to lend a helping hand. 