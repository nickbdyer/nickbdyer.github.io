---
layout: post
title: "Ventiquattro"
---

# Non-Bouncy Numbers

### Description

This is taken from a CodeWars kata, but also seems to be very similar to
project Euler problem 113.

Let's define increasing numbers as the numbers whose digits, read from left to
right, are never less than the previous ones: 234559 is an example of
increasing number.

Conversely, decreasing numbers have all the digits read from left to right so
that no digits is bigger than the previous one: 97732 is an example of
decreasing number.

Now your task is rather easy to declare (a bit less to perform): you have to
build a function to return the total occurrences of all the increasing or
decreasing numbers below 10 raised to the xth power (x will always be >= 0).

To give you a starting point, there are a grand total of increasing and
decreasing numbers as shown in the table:

| Total	| Below |
| ------------- |
| 1	    | 1 |
| 10	  | 10 |
| 100	  | 100 |
| 475	  | 1000 |
| 1675	| 10000 |
| 4954	| 100000 |
| 12952	| 1000000 |

<br>
Tips: efficiency and trying to figure out how it works are essential: with
a brute force approach, some tests with larger numbers may take more than the
total computing power currently on Earth to be finished in the short allotted
time.

Trivia: just for the sake of curiosity, a number which is neither decreasing of
increasing is called a bouncy number, like, say, 3848 or 37294; also, usually
0 is not considered being increasing, decreasing or bouncy, but it will be for
the purpose of this kata.

### Solution

Despite the advice in the question, significantly more time than was necessary
was spent trying to build a brute force solution. Having not really understood
the bounds of what brute force meant, I spent some time trying to memoise and
optimise a solution. However, it wasn't until I realised that the solution
should work for 10^100+ that it became obvious that even with solutions like
this it would take too long. Enter, Maths.

I broke the solution down into increasing and decreasing parts. 

Firstly increasing:

If we take increasing numbers less than 10^3 (1000), we are dealing with
3 digit numbers, and given any 3 digit number it can be arranged in 6 different
ways:

`456  465  564  546  654  645`

From that we can see that for increasing numbers there is only one solution
**456** that is increasing for a given choice of 3 numbers. This is important
because we can deduce that we are dealing with Combinations rather than
Permutations.  Specifically we are looking combinations with repetition, since
any number can be repeated in the selection of numbers. For help on the
difference take a look at this
[link](https://www.mathsisfun.com/combinatorics/combinations-permutations.html).

Since we know it is combinations with repetition we can simply apply the
formula:

                               (n+r-1)!
                               --------
                               r!(n-r)!

Where n is the number of digits available, and r is the number of digits we are
choosing. Specifically this includes 000, which is important for later. So
for numbers under a 1000, this can be expressed as 12 choose 3, which equals
220.

Decreasing numbers are a little more tricky, for some numbers it is simple
since it appears very similar to increasing numbers. However, issues arise when
you deal with leading zeroes. For example where 045 is an acceptable increasing
number, 054 would not be caught as a decreasing number, but actually it should.
If instead of leading zeroes we consider leading 'spaces', we actually create
another digit to choose from. This means we are now calculating 13 choose 3.

I'd like to explain that a bit further, since that solution did not come to be
me immediately. The problem with considering leading zeroes, is that when you
look at the ways to arrange a selection of 3 digits, you can end up with
a situation like this:

`001 100 010`

All three of these permutations should be considered decreasing numbers, but it
is difficult to see how to modify the equation to accomodate this. If we change
the leading zeroes to spaces though, it becomes a little clearer, and actually
we now have 3 different selections of digits:

`..1 100 .10`

And the good thing is, none of them can be arranged differently to make a valid
number, so we are back to our combination with repetition equation, but with
one extra digit, the space.

The only extra consideration we have is that there are now a number of space
only selections that are invalid:

`.00 ..0 ...`

Since all three of these are trying to express 000, we can remove them. But
notice, for 10^n (10 to the power of n), there will be n invalid numbers. So
the formula for decreasing numbers is:

                               (n+r-1)!
                               -------- - r
                               r!(n-r)!
                    
Finally there is some clean up to do, since our calculation has some
duplication. Not only have we counted 000 twice, but we have counted 1, 11,
111, 2, 22, 222 etc. twice. 

There is a formula here too, since for each power of 10, there are 9 new
duplications between the increasing and decreasing calculations. 

So we simply say lets remove (9 * r) + 1 from our total. Plus one, to account
for the single extra 000 that we calculated.

The final formula then looks like:

                    (n+r-1)!   (n+r-1)!
                    -------- + -------- - r - ((r*9) + 1)
                    r!(n-r)!   r!(n-r)!

Thankfully, this will calculate for number of non-bouncy numbers under 10^213
in less than a second, which is preferable over the brute force timeline...

