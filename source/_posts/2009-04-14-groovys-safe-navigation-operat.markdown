---
layout: post
title: Groovy's Safe Navigation Operator Not as Safe as I Thought
date: 2009-04-14 06:50:00 -04:00
comments: false
tags: groovy
---
The safe navigation operator is almost certainly my favorite operator in Groovy. It allows you to guard against `NullPointerException`(s) much more cleanly than defining a nasty if/else mess. Consider the following example.

``` groovy
class Book {
	String title
	Author author
}

class Author {
	String firstName
	String lastName
}

def author = new Author(firstName:'Jason')
def book = new Book(title:'Say no to NPEs', author:author)

println book.author?.lastName
```

Did you see that `?.` operator on the last line of the code sample? That's the safe navigation operator, and it's the equivalent of writing the following if statement (although it's much more readable).

``` groovy
if (book.author != null) {
	println book.author.lastName
}
```

The safe navigation operator essentially checks to see if the object you're about to invoke a method on is `null`. If it is `null`, it simply skips the method invocation and returns `null`. If it isn't `null`, it proceeds as usual. It works for method chaining too, so if you're worried about `book` being `null`, you could write

``` groovy
println book?.author?.lastName
```

This way, if either `book` or `author` are `null`, you simply print out `null` instead of causing a NullPointerException.

Since learning about the safe navigation operator, I've used it successfully in dozens of classes without any problem. Until last week. Consider the following line of code .

``` groovy
println book?.author?.firstName?.trim().concat(" is great.")
```

Thanks to the safe navigation operator we can write this concatenation of `firstName` and a sentence fragment without worrying about `NullPointerException`(s) and ugly if blocks. Or so I thought.

Looking at this line of code, I thought for sure I was safe from any sneaky `NullPointerException`. If `book`, `author` or `firstName` are `null` I'll simply end up printing `null` and not have to worry about the `concat()` method. After all, if the `trim()` method succeeds, there's no sense in guarding it's result for `null`. And that's where I was wrong.

I naively assumed the method chain would stop once the safe navigation operator encountered a `null` when in fact it does not. While the `trim()` method is safe from being called on a `null` `firstName` object, it will return `null`, upon which the `concat()` method is called. Oops! 
