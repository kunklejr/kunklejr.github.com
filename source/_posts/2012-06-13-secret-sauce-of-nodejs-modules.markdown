---
layout: post
title: Secret Sauce of NodeJS Modules
comments: true
tags: nodejs javascript
---
When I first starting learning about Node.js I read lots of blog posts and tutorials filled with examples. After all, there weren't any published books or comprehensive guides at the time. With my primary JavaScript frame of reference being its use in the browser, one thing that always puzzled me early on is how Node could possibly prevent me from creating global variables. Consider the following contrived code example.

``` js
function sayHello(name) {
	console.log('Hello there ' + name);
}

exports.greet(name) {
	sayHello(name);
}
```

If the above code were run in a browser I would have created a global function named `sayHello` and attached a function named `greet` to a global `exports` object. But that isn't what happens at all when using Node.

After some digging I came to learn that Node uses the [CommonJS](http://www.commonjs.org/) module pattern. Reading about CommonJS you'll learn that there's a magic `exports` variable available to your scripts, and anything attached to it is exposed to other code `require`'ing your script. That's all well and good and I understood the concept, but it didn't help me understand how it actually worked under the covers. How did it prevent functions like `sayHello` from becoming global and just where did that `exports` variable come from? So I resorted to digging through the Node source to find out what's really going on.

Fortunately, it didn't take long for me to stumble upon some code in `src/node.js`; It might have even been the first file I opened. On line 528 (Node version 0.6.18) you'll find the chunk of code shown below. Start reading at the compile function near the bottom. The first two things `compile` does is get the source of your script and then wrap it. Notice the `NativeModule.wrap` function is actually wrapping your entire source file in an anonymous function expression, exposing `exports`, `require`, `module`, `__filename`, and `__dirname` variables to your code. The wrapped source is then invoked in the `compile` function.

``` js
NativeModule.wrap = function(script) {
  return NativeModule.wrapper[0] + script + NativeModule.wrapper[1];
};

NativeModule.wrapper = [
  '(function (exports, require, module, __filename, __dirname) { ',
  '\n});'
];

NativeModule.prototype.compile = function() {
  var source = NativeModule.getSource(this.id);
  source = NativeModule.wrap(source);

  var fn = runInThisContext(source, this.filename, true);
  fn(this.exports, NativeModule.require, this, this.filename);

  this.loaded = true;
};
```

The important part to take away from this is that Node does not run exactly what you've written in your source file; it runs a wrapped version of your source. This wrapped function expression is the tactic used to prevent your top level functions and variables from becoming global. It's also how your code gets access to the magic `exports` variable, among others.

