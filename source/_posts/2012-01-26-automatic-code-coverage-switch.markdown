---
layout: post
title: Automatic Code Coverage Switching for Vows.js
date: 2012-01-26 15:22:16 -05:00
comments: true
tags: nodejs
---
 If you've used [Node.js](http://nodejs.org) lately you've likely run across [Vows](http://vowsjs.org/), the asynchronous behavior driven development test framework for Node. Vows does a really nice job of forcing you to separate your setup logic from your assertions, but that's not what this post is about.

Vows can generate test coverage reports with its --cover-plain, --cover-html, and --cover-json options. Unfortunately, creating code coverage metrics isn't as straightforward as simply including these options in your call to vows. You need to

1. Pre-instrument your code using [node-jscoverage](https://github.com/visionmedia/node-jscoverage).
2. Require the instrumented versions of your code from your tests.

The first prerequisite is fairly easy to satisfy, although you need to remember to re-run node-jscoverage any time you change the source files you're interested in covering. Setting up a script using something like [watchr](https://github.com/balupton/watchr) to re-instrument your files if anything changes might be a good option. Anyway, simply executing the following (assuming you've installed node-jscoverage and added it to your path) from the root of your node project will create the instrumented versions of the files in your lib directory:

``` bash
node-jscoverage lib lib-cov
```

The second prerequisite is not as straightforward to resolve as you might expect unless you want to generate the code coverage on every test run. Why? Because requiring the instrumented code from your test is not the same as requiring the un-instrumented versions. Consider

``` js
// test/myfile_test.js
var fut = require('../lib/myfile.js'); // regular version
var fut = require('../lib-cov/myfile.js'); // instrumented version
```

Since the statement requiring the file under test is in every test you write, having to change it when you want to run the instrumented or un-instrumented files is, in my opinion, not an option. So, on my current project we wrote the following javascript to to serve as a replacement to requiring your files under test.

``` js
// test/coverage.js
var covererageOn = process.argv.some(function(arg) {
  return /^--cover/.test(arg);  
});

if (covererageOn) {
  console.log('Code coverage on');

  exports.require = function(path) {
    var instrumentedPath = path.replace('/lib', '/lib-cov');

    try {
      require.resolve(instrumentedPath);
      return require(instrumentedPath);
    } catch (e) {
      console.log('Coverage on, but no instrumented file found at ' 
        + instrumentedPath);
      return require(path);
    }
  }
} else {
  console.log('Code coverage off');
  exports.require = require;
}
```

In essence, the script checks the process arguments to see if one of the vows --cover-* arguments were passed and changes its require method to substitute /lib-cov for any /lib references. If none of the coverage arguments are detected it simply requires the file as usual. Since your files under test normally use relative references to other files in your lib directory, they get covered too. Check out the example below to see how you would use the script in a test.

``` js
// test/mytest.js
var vows = require('vows');
var assert = require('assert');
var coverage = require('coverage');
var fileUnderTest = coverage.require('../lib/myfile.js');

vows.describe('your test').addBatch({
    // do some testing
}).export(module);
```
