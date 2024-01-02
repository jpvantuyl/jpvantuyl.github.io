---
layout: post
title: "Primes and Number Lines"
date: 2023-10-18 -0800
comments: false
categories: [math, prime, binary]
---

We always use base-10 for our number lines.  It helps us think about things.  Last night I couldn't sleep because I was imagining that each prime number had its own number line where it was the base.  The beautiful part was that it met the definition of a prime number which is that the number is divisible only by itself and 1.

For example, two is a prime number.  In base-2 (aka binary) that is `0010`.  All other even numbers aren't prime because they're divisible by two (e.g. `0100`, `1000`, `0001 0000`, etc.)  The pattern I liked here was those zeros padding the right part of the number.  Each one represented another number that the Sieve of Erathosthenes knocked out.

Here's a handful of primes in decimal, binary, and their own base:

|Decimal|binary|base-prime|
|-|-|-:|
|2|`0000 0001`|0 1|
|3|`0000 0011`|0 2|
|5|`0000 0101`|0 4|
|7|`0000 0111`|0 6|
|11|`0000 1011`|0 10|
|13|`0000 1101`|0 12|
|17|`0001 0001`|0 16|
|19|`0001 0011`|0 18|
|23|`0001 0111`|0 22|
|29|`0001 1101`|0 28|
|31|`0001 1111`|0 30|
|37|`0010 0101`|0 36|
|41|`0010 1001`|0 40|
|43|`0010 1011`|0 42|
|47|`0010 1111`|0 46|
|53|`0011 0101`|0 52|
|59|`0011 1011`|0 58|
|61|`0011 1101`|0 60|
|67|`0100 0011`|0 66|
|71|`0100 0111`|0 70|
|73|`0100 1001`|0 72|
|79|`0100 1111`|0 78|
|83|`0101 0011`|0 82|
|89|`0101 1001`|0 88|
|97|`0110 0001`|0 96|
|101|`0110 0101`|0 100|
|103|`0110 0111`|0 102|
|107|`0110 1011`|0 106|
|109|`0110 1101`|0 108|
|113|`0111 0001`|0 112|
|127|`0111 1111`|0 126|
|131|`1000 0011`|0 130|

I like the _symetry_ of shifting each prime number into its own base because except for the first prime number they're all even numbers. In the regular list of primes everything is odd.  My brain wants there to be a pattern.  Heck, maybe there _is_ a pattern but I didn't take enough math in school to get to it.
