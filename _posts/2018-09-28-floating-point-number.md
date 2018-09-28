---
layout: post
title: Play with floating point number
tags:
- Algorithm
---

# Represent a fraction number in binary

With similar calculation as integer number, we can represent real number in binary form:

![Binary](/images/binary-fraction.png)

Here the real number `13.6875` has its binary form `1101.1011`

Now, for simplicity, we just care about the fraction part (to the right of radix point)

Some example of numbers:
```
Fraction   Decimal   Binary
---------------------------
1/2        0.5       0.1
1/4        0.25      0.01
1/8        0.125     0.001
1/16       0.0625    0.0001
```
Those example can be represented exactly in binary form without any error.

How about `1/3`?
```
Fraction   Decimal     Binary
------------------------------------
1/3        0.333...    0.01010101...
```
As you can see, `1/3` can't be accurately represented in both decimal and binary form.  
We have some error, like `0.000333...` in decimal form.

Q: Can we just store `1/3` in some convention form like `1` and `operation /` and `3`?  
A: Absolutely, but not in scope of this post.

Here, we accept that in order to store `1/3` in computer, we have to accept some error.  
We can minimize the error by increasing the data structure size. For example (very naive and not real):

- 8 bits number `0.01010101`, so error is `0.000333...`
- 16 bits number `0.0101010101010101`, so error is `0.000000333...`
- ...

## A bit analysis about `1/3`

We can see a pattern here:

```
1/3 = 1/4  + 1/16   + 1/64     + ...
    = 0.01 + 0.0001 + 0.000001 + ...
    = 0.010101                 + ...
```
That's how computer represents `1/3`.

We can continue forever and get the closest value to `1/3`, but not exactly `1/3`.  
Depend on how much memory we use to store number, we can keep the error more or less.

## Store in computer

Can we just store that number in binary form to the memory?  
Yes, but there is a better way to do it: floating point number.


# Floating point number

Any real number (`1.2345` in this case) can be rewritten as:

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/ae814346939ac31086e1d0286c41d98e6b053102)

Term **floating point** means the radix point can be floating or moving to left/right, depends on the base and exponent.

How to store number in that format?  
There is a standard: IEEE Standard 754.

Example memory layout using 32bits to store float number:
```
SEEEEEEE EFFFFFFF FFFFFFFF FFFFFFFF
S: sign (1bit)
E: exponent (8bits)
F: fraction or significand (23bits)

Note: here we use base 2
```

This [article](https://steve.hollasch.net/cgindex/coding/ieeefloat.html) describes very detail. Read it and comeback.

# IEEE 754: Analyze the limit

Here, we will find maximum number that is supported by 32bits floating point number, this format:
`SEEEEEEE EFFFFFFF FFFFFFFF FFFFFFFF`

## Sign (1bit)

Not important

## Exponent (8bits)

With 8bits, max value is 127. So the max scaling: `2^127`

## Fraction (23bits)

The fraction has 23bits, combine with 1 hidden bit value `1` (depends on system implementation, read the above link),
so the fraction should be:
```
Fraction:  1.FFFFFFFFFFFFFFFFFFFFFFF

Max value: 1.11111111111111111111111
Decimal:   1.99999988079071044921875
```
Note: you should use [this tool](https://www.exploringbinary.com/binary-converter/) for precisely conversion.

## Sum up

So, max value of 32bits floating point number is:

```
   fraction                    x   base^exponent
 = 1.99999988079071044921875   x   2^127
~= 3.4028235                   x   10^38
```

If we use more bits floating point number:
```
32bits (7 digits precision):
  3.4028235          x 10^38

64bits (16 digits precision):
  3.4028234663852886 x 10^38

Exact  (38 digits precision):
  3.40282346638528859811704183484516925440 x 10^38
```

# Precision test

In JAVA, we know:

- `float` is 32bits fp number (7 digits precision)
- `double` is 64bits fp number (16 digits precision)

We will make some tests.

## Make change inside of precision range

```
float a = 1.2222333f;
float b = 0.0000001f;
float c = a + b;

System.out.println("a    : " + a);
System.out.println("b    : " + b);
System.out.println("c    : " + c);
System.out.println("a==c : " + (a==c));

---- Output ----
a    : 1.2222333
b    : 1.0E-7
c    : 1.2222334    # the addition is correct
a==c : false        # the comparision is correct
```
We can see `b = 0.0000001` is inside precision range (7 digits), so the calculation is simply correct.

## Make change outside of precision range

```
float a = 1.2222333f;
float b = 0.00000001f;
float c = a + b;

System.out.println("a    : " + a);
System.out.println("b    : " + b);
System.out.println("c    : " + c);
System.out.println("a==c : " + (a==c));

---- Output ----
a    : 1.2222333
b    : 1.0E-8
c    : 1.2222333   # oops, seem like we didn't add
a==c : true        # ?
```
Here, the change amount `b = 0.00000001` is outside of precision range (8th digit).  
`c` can be like `1.22223331` but it prints just `1.2222333`.  
And `a==c is true`.

Does it just discard the addition if the operand is outside of precision range? NO.

## Make big change outside of precision range

Now we make bigger change: `0.00000009` instead of `0.00000001`
```
float a = 1.2222333f;
float b = 0.00000009f;  // Instead of just 0.00000001
float c = a + b;

System.out.println("a    : " + a);
System.out.println("b    : " + b);
System.out.println("c    : " + c);
System.out.println("a==c : " + (a==c));

---- Output ----
a    : 1.2222333
b    : 9.0E-8
c    : 1.2222334   # oops, it's correct again
a==c : false       # great!
```
We can see it doesnt discard operations outside of precision range. The calculation is correct.  

The problem here is when STORING value into variable, it ROUNDS UP to keep only 7 digits precision.

In practice, this whole process is performed by floating point arithmetic.

Here, for intuition understanding, I simplify as:
```
Step 1: Expand precision range (e.g. 1.2222333 -> 1.2222332954406738)
Step 2: Do calculation to get result (e.g.        1.2222333854406742)
Step 3: Rounds up result to 7-digits (1.2222334) 
```

In short: The calculation result is precise. The stored result isn't.

# Reference

- [Good IEEE 754 summary](https://steve.hollasch.net/cgindex/coding/ieeefloat.html)
- [Short explain storing in mem](https://softwareengineering.stackexchange.com/a/215126)
- [Manually calculate 0.1 + 0.3](https://softwareengineering.stackexchange.com/questions/310623/floating-point-calculation-being-too-exact)
- [Very detail explanation](http://blog.reverberate.org/2014/09/what-every-computer-programmer-should.html)