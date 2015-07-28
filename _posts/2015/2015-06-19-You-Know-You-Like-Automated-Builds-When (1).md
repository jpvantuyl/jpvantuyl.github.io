---
layout: post
title: "You know you love automated builds when..."
date: 2015-06-19 -0800
comments: true
categories: [builds, automation, ruby, jekyll, travis-ci, linux]
---

I can't help it.  I know that I should be writing blog entries.  I've got a ton of ideas stuck in my head.  But every time I sit down to write, I notice that the build for this site is broken.

[![Build Status](https://travis-ci.org/jpvantuyl/jpvantuyl.github.io.svg?branch=master)](https://travis-ci.org/jpvantuyl/jpvantuyl.github.io)

Having a working build is almost stupidly important to me.  It's like software isn't software until an automated build runs some unit tests and declares success.  Even for a web page.

Jekyll pages on Github can integrated pretty cleanly with Travis-CI.  There's even helpful [documentation](http://jekyllrb.com/docs/continuous-integration/) to get you started.  The HTML-Proofer gem crawls your generated static HTML site and points out things that you should fix like images without alt tags, bad css references, etc.

I'll admit that it took me weeks of trying off and on to even get to a build with failing tests.  All hail build [#14](https://travis-ci.org/jpvantuyl/jpvantuyl.github.io/builds/67619155).  It really just took time to learn how to install and setup Ruby and Jekyll in Linux.  Piece of cake.  No, really.  Cake.

So now I've got 62 errors in the markup generated from my markdown (and Jekyll templates) to fix.  Does that make me a happy camper?  Well, kindof.  It makes me **less unhappy**.  That should count for something.  And I suspect I'll feel another sense of accomplishment when I get a clean build.  Red, Green, Refactor is supposed to work like that.