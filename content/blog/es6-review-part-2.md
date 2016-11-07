---
title: ES6 Review Part 2
date: 2015-07-20
tags: Javascript, ES6
---


This is a second review post for new ES6 features that I'd love to share. You
can find the first review here [ES6 Review Part 1](/blog/es6-review-part-1/)

### Arrows
I have been using arrow (->) and fat-arrow (=>) key in Coffeescript for while, and I was happy when ES6 would support arrow key (=>) as a short-hand of function keyword. Of course, if you’re familiar with arrow keys in Coffeescript, this feature will be similar. Here are some common use cases:

      $(“button”).on(“click”, (event) => {
        runApp();
      });

      var sum = (x, y) => { return x + y; };
      let x = 2;
      let y = 3;
      sum(x, y); // return 5


### Strings
ES6 provides a better way to work with Strings, such as multiline strings, or string interpolation.

      `Multiline javascript
        strings`

      `${x} + ${y} = ${sum(x, y)}`


### Better managing your javascript files with modules
With ES6 Modules, it’s much easier to organize your javascript files across the app. For instance:

      // lib/utility.js
      export function sum(x, y) {
        return x + y;
      }
      // app.js
      import * as utility from “lib/utility”;
      console.log(`2 + 3 = ${utility.sum(2, 3)}`)

Of course, there are more exciting features coming in ES6. I don’t tend to review them all, instead the one that I found myself using most of the time. To see other features, you can visit this [Github page](https://github.com/lukehoban/es6features#template-strings).
