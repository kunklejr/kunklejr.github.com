---
layout: post
title: Lessons Learned Building an HTTPS Everywhere Safari Extension Clone
date: 2010-12-14 08:31:55 -05:00
comments: false
tags: javascript macosx security
---
Ever since hearing about [Firesheep](http://codebutler.com/firesheep) I've wanted a Safari extension similar to the [HTTPS Everywhere](https://www.eff.org/https-everywhere) extension for Firefox. Frankly, I was puzzled that one didn't already exist, so I set out to write it. The result is [SSL Everywhere](http://www.nearinfinity.com/home/opensource/ssl-everywhere.html). The journey is this blog post.

I started the project as an effort to protect myself and others from Firesheep when using Safari on an open public wireless network, much like those found in coffee shops, hotels, and airports everywhere. Firesheep works by [hijacking your session](https://secure.wikimedia.org/wikipedia/en/wiki/Session_hijacking), which is basically a way of stealing your active login without needing your username or password. 

My goal with SSL Everywhere was to protect someone from a session hijacking attack by ensuring all requests to the originating website were tunneled through SSL. This seemed trivial until I thought about the various kinds of requests that would need to be secured.

* the original HTML page
* images
* external JavaScript files
* external CSS files
* inline frames (iframes)
* object or embed tags for things like videos or applets
* Ajax requests
* requests for the favicon

It wasn't until I started to tackle each of the list items that I realized just how limited Safari's extension API is, and ultimately that **one could never build a foolproof extension to protect Safari users from session hijacking attacks**.

Lessons Learned
===============
If you've spent any significant time writing Safari extensions, you may already be aware of the many restrictions and challenges I wrestled with for hours. Much of what I learned was trial and error, despite [well written documentation from Apple](http://developer.apple.com/library/safari/#documentation/Tools/Conceptual/SafariExtensionGuide/Introduction/Introduction.html). While the documentation does a fair job of describing many of the things you can do with an extension, it doesn't provide as much detail on what you _can't_ do. That's what you'll find below.

No Access to Raw Cookies
------------------------
The key to avoiding a session hijacking attempt is making sure the attacker can't access the session cookie, or cookies, your browser sends on each request. This can obviously be done by making sure all requests take place over an SSL connection. Unfortunately, you can't guarantee this won't happen since Safari extensions have no opportunity to intercept webpage requests before they occur. A user could easily type http://twitter.com into their address bar and SSL Everywhere couldn't stop the request.

The other option I pursued was having SSL Everywhere attempt to mark session cookies as secure since the browser will not send cookies marked as secure over a non-SSL connection. However, a Safari extension has no additional ability to manipulate cookies than normal JavaScript running on a page, meaning there is no way to read a cookie's path or expiration date for example. So, there's no way for the SSL Everywhere extension to simply mark a cookie as secure without having to guess at, or omit, other cookie information which may be important to the operation of the website.

No beforeload for favicons, Ajax Calls, or Stylesheet References
----------------------------------------------------------------
Apple was responsible for adding the beforeload event to Webkit. This gives scripts (and extensions) the ability to decide whether an external resource should be loaded or not. As stated in the Safari Extensions Development Guide, 

> Safari 5.0 and later (and other Webkit-based browsers) generates a "beforeload" event before loading each sub-resource belonging to a webpage. The "beforeload" event is generated before loading every script, iframe, image, or style sheet specified in the webpage, for example.

I was thrilled after reading those two sentences because it was exactly what I needed. That is, until I discovered there's no beforeload event fired when the browser requests a website's favicon, makes an Ajax request, or loads an image reference defined in a stylesheet. This leaves a loophole where I can't stop a non-secure favicon reference, Ajax call, or CSS image reference from exposing the session cookies I can't properly mark as secure.

src Attribute Can Not be Changed in beforeload Event Handlers
-------------------------------------------------------------
I ultimately wanted to have a beforeload event listener that would 

1. inspect the source URL being requested
2. rewrite the source URL to a secure https: version if necessary
3. replace the resource's insecure reference with the secure https: URL
4. allow the loading of the resource to proceed with the new, secure URL

Following the above procedure results in the resource being requested with the SSL-secured URL as desired, but will not stop the original load with the original, insecure, URL. You just end up making two requests for the same resource; one over SSL, the other not. Again, another point of exposure for the cookies.

beforeload != before request
----------------------------
It turns out that all the effort described above to leverage the beforeload event was futile. Preventing a resource from loading, as described in Apple's example of [blocking unwanted content](http://developer.apple.com/library/safari/documentation/Tools/Conceptual/SafariExtensionGuide/MessagesandProxies/MessagesandProxies.html#//apple_ref/doc/uid/TP40009977-CH14-SW9), stops it from being inserted into the DOM, but does not prevent it from being requested by the browser anyway.

Apple's example documentation states

> If your script responds to a "beforeload" event by calling event.preventDefault(), the pending sub-resource is not loaded. This is a useful technique for blocking ads...

Should I have really expected that "the pending sub-resource is not loaded" doesn't imply that it's not requested? Perhaps I wasn't sharp enough to catch that nuance, but once again the cookies are exposed.

Host Page Prototypes Can Not be Changed
---------------------------------------
When I discovered the beforeload event doesn't fire on Ajax requests, I decided to try to override the XMLHttpRequest `open` method and rewrite any insecure URLs to a secure version before yielding to the original `open` implementation. What I found was that you can't modify the prototypes of the page your start or end scripts are being injected into.

I suspect this has something to do with start and end scripts being secluded in their own separate, and randomly named, namespace. Changing around object prototypes never resulted in any errors, it just didn't change the prototypes of the host page and therefore couldn't change the XMLHttpRequest object. Again, another point of exposure for the precious cookies.

Status of SSL Everywhere
========================
If you've read this far it should be obvious that SSL Everywhere cannot guarantee protection against session hijacking attacks, including those from Firesheep. However, it does enhance security by automatically redirecting you to secure versions of many websites and rewriting insecure links to their SSL-encrypted equivalents. If you must use Safari to access popular websites when connected to an open WiFi network, you're probably better off doing it with SSL Everywhere.

We don't offer a pre-built version of the extension for easy installation into Safari because we don't want people to casually install it and forget that it's not completely secure for all sites. If people find it valuable nonetheless, then we may consider creating the extension bundle in the future.

Try It!
=======
If you'd like to try SSL Everywhere you can find the [source code on Github](https://github.com/nearinfinity/ssl-everywhere.safariextension). It's open source software licensed under the GPL version 2 license, primarily because it borrows code from [HTTPS Everywhere](https://www.eff.org/https-everywhere). If you can come up with solutions to any of the problems I encountered, please let me know! 
