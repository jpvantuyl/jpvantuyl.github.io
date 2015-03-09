---
layout: post
title: "Battery Staple Redux"
date: 2014-04-14 -0800
comments: false
categories: [security]
---

Dicewords passwords (the [correct horse battery staple](https://johnvantuyl.com/correct-horse-battery-staple/) mentioned previously) should be much longer than 4-5 words.  That's because hackers use [hordes of compromised computers](http://arstechnica.com/security/2012/12/25-gpu-cluster-cracks-every-standard-windows-password-in-6-hours/) to throw billions of guesses per second at the problem.  So the number of possibilities of your funkalicious password choosing algorithm has to be subtantially more than what an attacker can guess.  That's the ratio you're managing.

Taking the 350 billion guesses per second in the above article one of my 5 word passwords would be cracked in about 22 hours.  Adding a sixth word extends the life to about 5 years.  Expanding the dictionary to the [dicewords list](http://world.std.com/~reinhold/diceware.wordlist.asc) of 7776 and adding a seventh word password would last [until about 2030](http://diceware.blogspot.com/2014/03/time-to-add-word.html).

Security professionals measure the strength of a password differently than computing the ratio that I've outlined above.  This helps as the password cracking power of attackers is constanly increasing.  Professionals measure password strength in *bits of entropy*.  You can compute the entropy bits by multiplying the number of words by `log2(N)` where `N` is the number of words in the dictonary.  My dictionary has about `10.9` bits of entropy and the dicewords list has `12.9` whereas normal written English is estimated at 1.3 bits per letter.