---
layout: post
title: Parsing pcap Files in Node.js with pcap-parser
date: 2012-01-12 08:00:00 -05:00
comments: true
tags: nodejs
---
On my current project we've been using [Node.js](http://nodejs.org) for an app that does a lot of packet capture and processing. In the past we used [node-pcap](https://github.com/mranney/node_pcap) for packet capture but were looking for an easier way to simply parse raw pcap files. It turns out the [pcap file format](http://wiki.wireshark.org/Development/LibpcapFileFormat) is pretty simple as was writing a node module to parse it.

The module, called [pcap-parser](https://github.com/nearinfinity/node-pcap-parser), can be used to parse any pcap file or readable pcap stream, such as the piped output of [tcpdump](http://www.tcpdump.org/). As packets are parsed, pcap-parser emits relevant events to which your node program can listen. It's a really simple way to process a theoretically infinite stream of pcap data. The code below shows a basic example of it in action. Please check out the [project page](https://github.com/nearinfinity/node-pcap-parser) for more details.

``` js
var pcapp = require('pcap-parser');

var parser = pcapp.parse('/path/to/file.pcap');
// var parser = pcapp.parse(process.stdin);

parser.on('packet', function(packet) {
  console.log(packet.header);
  console.log(packet.data);
});
```
