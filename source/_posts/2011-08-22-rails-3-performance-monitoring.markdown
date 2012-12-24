---
layout: post
title: Rails 3 Performance Monitoring with System Metrics
date: 2011-08-22 09:42:18 -04:00
comments: false
tags: ruby rails
---
System Metrics is a new Rails 3 Engine providing a clean web interface to the performance metrics instrumented with `ActiveSupport::Notifications`. It will collect and display the notifications baked into Rails and any additional custom ones you add using `ActiveSupport::Notification#instrument`.

System Metrics is not intended to be a replacement for performance monitoring solutions such as New Relic. However, it is especially handy for quickly identifying performance problems in a development environment. It's also a great alternative for private networks disconnected from the Internet.

You can find more information about System Metrics on the [System Metrics site](http://nearinfinity.github.com/system-metrics). Please kick the tires and let us know what you think.

![System Metrics Detail View](/assets/smdetails.png) 
