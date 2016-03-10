---
layout: post
title:  "Check if number is a power of 2"
date:   2016-03-10 3:55:35 +0530
categories: jekyll update
---
The intution for this solution was when i was trying my hands on the [Collatz conjecture][collatz-conjecture-wiki], which basically is to get the series for a given number(n) by doing `n/2` if even and `3n+1` if odd. Its found that the series always stops with the continuos sequence `4-2-1`. For example consider the series for 21 would be `21-64-32-16-8-4-2-1-4-2-1...`. There is no proof for this but for the largest number they could compute they found that the sequence always ends with `4-2-1`. So how is this related to even power of 2, getting there, patience.

Now while thinking about the problem you find that for the series to converge to 4-2-1, odd `n` should either reach to 4 or 16 or 64 ..., why not 8, 32 ? for n to reach 8,32 its predecessor in the series should be 7/3 or 31/3 which is not possible. So we observe that even powers of 2 can be written as 3n+1. This can be proved easily.

{% highlight ruby %}

consider 2^(2n+2)
=> 4*(2^2n)
=> 3(2^2n) + (2^2n)
=> 3(2^2n) + 3*(2^(2n-2)) +.....+ 4
=> 3(2^2n + 2^(2n-2) + ... + 1) + 1
=> 3x + 1
{%endhighlight%}

So any even power of 2 can be written as `3n + 1`

Now given any number `n` and we have to check if the number is a power of 2 or not, then we can square the number, substract one and check reminder with 3 is zero or not.

If u want to know more about Collatz conjecture, check out the [youtube video][collatz-conjecture-youtube]

[collatz-conjecture-wiki]: https://en.wikipedia.org/wiki/Collatz_conjecture
[collatz-conjecture-youtube]: https://www.youtube.com/watch?v=m4CjXk_b8zo

