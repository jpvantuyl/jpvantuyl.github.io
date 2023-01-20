---
layout: post
title: "Passphrase Generator"
date: 2023-01-19 -0800
comments: false
categories: [password,passphrase]
---

Ever since XKCD came out with the `correct horse battery staple` I've moved to using passphrases everywhere.

Now that I've got kids I use them for homework assignments that I generate mere moments before they get off the bus.  They need to practice reading, writing, and arithmatic.

Here's a passphrase generator using the "ten hundred" most popular words that Randall Munroe used in his [Thing Explainer](https://xkcd.com/thing-explainer/) book.

<passphrase style="font-family:monospace;white-space:pre;font-size: xxx-large;border: solid black 3px;padding: 8px;margin: 10px;display: inline-block;border-radius: 10px;">huh ever able clothes</passphrase><br />
<input style="font-size: larger;background-color: #4466c5;padding: 10px;border-radius: 10px;border: none;padding: 8px;margin: 10px;display: inline-block;color: #ddd;" type="button" onload="nextPassphrase()" onclick="nextPassphrase()" value="Next">
