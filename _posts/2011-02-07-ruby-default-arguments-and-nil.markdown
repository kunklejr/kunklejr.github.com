---
layout: post
title: Ruby Default Arguments and nil
date: 2011-02-07 09:00:00 -05:00
comments: false
tags: ruby
---
 Ruby's default arguments are a really handy language feature that I recently discovered aren't as handy as I once thought. I was trying to track down an error I was getting in the following snippet of code in my ActiveRecord model object:

``` ruby
def as_json(options={})
  options[:methods] ||= []
  options[:methods] << :full_name
  super(options)
end
```

When this method was executed in order to convert the object into a JSON representation, I received this error (with line numbers adjusted for this example):

``` console
NoMethodError: undefined method '[]' for nil:NilClass
  from MyModel:2:in 'as_json'
```

I stared at this for a while until it dawned on me that explicitly passing 'nil' to as_json might not result in options being set to the default empty hash, and indeed it does not. I suppose it's technically correct, since nil is an instance of NilClass in Ruby and not some special value as it is in many other languages. It's just not what I was expecting and not what I want. For all the books, blog posts, and tutorials I've read, you'd think I would have picked up on this somewhere along the way.

Curiously though, can anyone think of a valid use case for not assigning the default value to a nil argument? I'm sure there are some, but they're not coming to mind.
