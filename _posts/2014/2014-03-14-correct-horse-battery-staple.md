---
layout: post
title: "Correct Horse Battery Staple"
date: 2014-03-14 -0800
comments: false
categories: [security]
---

I like [password managers](http://arstechnica.com/information-technology/2013/06/the-secret-to-online-safety-lies-random-characters-and-a-password-manager/).  I've been using [KeePass](http://keepass.info/) for a couple years now and it has never done me wrong.  Storing passwords in a password manager allows you to pick passwords like `sir35zf58u` without having to remember all that gobbledygook.  The catch is that you have to remember the password to your password manager which is where the `Correct Horse Battery Staple` comes in.

![Password Strength - xkcd.com](/images/content/2014/Aug/password_strength.png)

The method goes something like this:

1. Find a dictionary.
2. Pick any four words *at random*
3. ...
4. Profit

I've used [Preshing's xkcd passphrase generator](http://passphra.se) which does exactly the above.  The dictionary is 1949 common words which is important for a number of reasons.  First, so you don't have to remember something like `ternion` or spell something like `sesquipedalian` every time you log in.  And second because the size of the dictionary you use and the words in it tell you how long a hacker would have to work to get your password.  If we got `Correct Horse Battery Staple` from [passphra.se](http://passphra.se) then it'd be one out of `1949^4=14,429,369,557,201` or about 14 trillion.

For a really important password like the master password on a password safe like KeePass you'd want to add in some additional complexity to make it harder to guess.  Picking a fifth word (e.g. `Correct Horse Battery Staple Supply`) would put you at `1949^5=28,122,841,266,984,749` or about one in twenty eight quadrillion.  You could also [try mis-spelling one word](http://arstechnica.com/security/2013/07/how-elite-security-ninjas-choose-and-safeguard-their-passwords/) (e.g. `Batery`), substituting letters and numbers (e.g. `B@tt3ry`), adding in some secret sauce of your own with random letters and numbers at the front, back or middle of your password (e.g. `Correct Horse Battery Staple Supply 4ti8R`).

Once you've gotten a complex but memorable password on your password safe then you can start generating complex passwords that you could *never* remember.  The thing that makes a password like `sir35zf58u` strong is it's length and it's character set.  This one is 10 characters long and uses lower case letters and numbers for a total of 36 characters to choose from.  For those that remember their combinatorics that's `36^10=3,656,158,440,062,976` or three and a half quadrillion possibilities.  I wanted to show you something with upper case letters as well but it was so much longer that I couldn't make sense of it.

That makes it tougher for hackers to get your password after they've gotten into [Adobe/Gawker/Forbes/Snapchat/Sony/Yahoo's](https://haveibeenpwned.com/PwnedWebsites)  database.  Even big, professional companies get compromised so it's important to make your passwords complex, secure, and to never re-use them.  I've got over one hundred websites stored in my password safe and I probably add another handful each month.