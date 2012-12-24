---
layout: post
title: NodeJS Test Coverage Reporting with CoverShot
comments: true
tags: nodejs javascript testing
---
Automated testing is a critically important practice at Near Infinity.
We try to automate every test we can and run them all the time as part
of a continuous integration process. But how do you know if you're tests
are any good? One method for doing so is reporting on your test
[code coverage](http://en.wikipedia.org/wiki/Code_coverage).

If you're using Node.js, there are [several](https://github.com/substack/node-bunker)
[tools](https://github.com/visionmedia/node-jscoverage) for instrumenting
your code for coverage analysis, but none provide a great output format
for easily viewing and navigating the results. That's why we decided to
create [CoverShot](http://nearinfinity.github.com/node-covershot/).

[CoverShot](http://nearinfinity.github.com/node-covershot/) is a
multi-format, test framework agnostic, code coverage
report generator for Node.js applications and modules. That's a mouthful
for saying it's a tool that generates nice code coverage output in
several formats, no matter which test framework you use. A sample of the
HTML summary page is shown below.

![Coverage Summary](/assets/nodejs_test_coverage_reporting_with_covershot/coverage.png "Coverage Summary")

Some of the most interesting features of CoverShot include:

- **Test Framework Agnostic** - It doesn't matter which test framework you
  use. In fact, your test framework won't even know that code
  coverage is being collected.
- **Multi-process Support** - Coverage will be collected even on
  code executed by spawned child processes.
- **Not Tied to Testing** - There's nothing about CoverShot that
  requires coverage collection to be initiated by tests. You can even use
  it to record coverage from your running application.

To get started, you'll need to install `covershot` and its dependencies.

``` console
$> npm install covershot
$> npm install jsmeter2
```

You'll also need to install `jscoverage`, which is not an npm package.
Please see [this](http://siliconforks.com/jscoverage/) or
[this](https://github.com/visionmedia/node-jscoverage) for installation
options.

The first step to beautiful code coverage reports is to instrument your
code using `jscoverage`.

``` console
$> jscoverage --no-highlight lib lib-cov
```

This creates a modified version of all the JavaScript files in the lib
directory and its subdirectories, instrumented for coverage collection.

Next, you'll need to change the way you require the files you want to
collect coverage on.

``` js
var csrequire = require('covershot').require.bind(null, require);

// coverage will be collected for this file and all files it requires
var myLibrary = csrequire('../lib/myLibrary');
```

If you'd like to collect some source code metrics too, run `jsmeter2`

``` console
$> ./node_modules/node-jsmeter/bin/jsmeter.js -o ./covershot/jsmeter/ ./lib/
```

Finally, use `covershot` to generate the report, in this case an HTML
report.

``` console
$> ./node_modules/covershot/bin/covershot covershot/data -f html
```

A static set of HTML pages will be generated in the covershot
subdirectory of your current working directory.

It's important to realize that good test coverage does not guarantee
error-free code. You can still [fail with 100% test coverage](http://jasonrudolph.com/blog/testing-anti-patterns-how-to-fail-with-100-test-coverage/).
It's simply another tool in the toolbox for helping you evaluate the
effectiveness of your tests. Please check it out and let us know how we
can improve CoverShot.
