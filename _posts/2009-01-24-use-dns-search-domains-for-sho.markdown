---
layout: blogs
title: Use DNS Search Domains for Shorter URLs
date: 2009-01-24 20:01:37 -05:00
comments: false
tags: general macosx
---
While I was reading through some Apple documentation on Bonjour I stumbled across a discussion on link-local addressing and DNS search domains. While the details of link-local addressing aren't that important here, the discussion on DNS search domains triggered a little light bulb in my brain.

You see, DNS search domains are used as a guide to help your computer lookup an IP address when something simple like "blogs" is typed into your browser's URL. If you setup a DNS search domain of "nearinfinity.com", typing "blogs" will force your computer to first check if there's an address for "blogs.nearinfinity.com". If not it falls back to its default behavior.

If you're a person that spends a lot of time on a small number of websites, you could use DNS search domains to your advantage to make navigating to those sites a snap. For instance, I just setup "nearinfinity.com" as an entry in my DNS search domains on my Mac. Simply go to the network preferences pane, click the advanced button at the bottom left, and then the DNS tab on the resulting popup.

![dnssearch](/assets/dns_search_domains_osx.jpg "DNS Search Domain")

So now, whenever I type "blogs" in my URL I get "blogs.nearinfinity.com". Whenever I type "support" I get "support.nearinfinity.com". I think you probably get the idea. I know I'm no genius for discovering this since it's precisely the reason for having search domains. I just never thought to use them before.
