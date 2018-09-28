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

# What programmer should be aware of?

In JAVA, we know:

- `float` is 32bits fp number (7 digits precision)
- `double` is 64bits fp number (16 digits precision)

## Store in memory

We know `0.1` can not be stored exactly in memory.  
Computer can only store the CLOSEST number to it.  
"How close" is depends on the type of variable.

### Use `float` type

When we use `float` type to store value: `float a = 0.1f`

- Computer will allocate 32bits memory slot
- Assign the CLOSEST floating point number which can be represented by 32 bits.
- It is: `00111101110011001100110011001101`
- That bits sequence is assigned to the variable

Analyzing that 32 bits sequence:
```
Sign: 0
Exponent: 01111011 or 2^(-4)
Fraction: 10011001100110011001101
Fraction with 1 leading 1:
         110011001100110011001101
```
We can convert that bits sequence to exact value in real life:  
`0.100000001490116119384765625` exactly

So now, the variable `a` has value `0.100000001490116119384765625` exactly

That is CLOSEST number can be stored to represent 0.1 in `float` type.

But when we print it out: `System.out.println("a = " + a)`, we see: `a = 0.1`  
It's because the precision of `float` is 7-digits, we can only see last 7 digit. The remaining
`1490116119384765625` will be cut out when displaying. But the memory still stores exactly value.

### Use `double` type

Now use `double` type to store the above 32-bits sequence.
```
double b = 0.1f;  // Notice the trailing f
System.out.println("b = " + b)

---- Output ----
b = 0.10000000149011612
```
We can see `double` uses 64bits to store that 32bits sequence.  
When printing, we can see precision 16-digits.

But don't stop here. Let's try this code:
```
double b = 0.1;   // Without trailing f
System.out.println("b = " + b)

---- Output ----
b = 0.1
```
In this code, `double` doesnt store our old 32bits sequence.  
Instead, computer uses 64 bits to store the CLOSEST number to 0.1 in 64bits, which is a number with very long decimal
fraction.

So when printing, that remaining is cut out, to only display `b = 0.1`

But in memory perspective, the variable `double b` is holding much more CLOSER number to `0.1`, than `float a`

### Summary

- Actual value of variable `float` or `double` is stored in binary form in memory
- Displaying (via `System.out.print`) doesnt represent actual value of variable
- If you want to print actual value, print it in binary form
- Arithmetic operations are based on actual value, not printed value

## Careful when working with floating point

```
float  a = 1.22223335f;
double b = a;
double c = 1.22223335;

System.out.println("a = " + a);
System.out.println("b = " + b);
System.out.println("c = " + c);

---- Output ----
a = 1.2222333
b = 1.2222332954406738
c = 1.22223335
```

Examine the `float  a = 1.22223335f`:

- Creates 32bits variable a
- The closest number to `1.22223335` in float type is `1.2222332954406738...`
- So it's stored in variable `a`
- Which is then printed `1.2222333` (rounded)

Examine the `double b = 1.22223335f`:

- Variable `b` stores the same memory content (bits sequence) as variable `a`
- So it represents the same number: `1.2222332954406738...`
- When printing, the trailing (`...`) is rounded. So it print `1.2222332954406738`
- So it's different when displaying the same number, depends on the type.

Examine the `double c = 1.22223335`:

- Here `c` will stores a difference memory content than above cases
- The memory content (64bits) holds much more closer number to `1.22223335` as compared with
above cases (`1.2222332954406738...`)
- It may be like `1.22223335000000000000000123...` (just for example)
- So when printing, the trailing digits is rounded and discarded, which leave us: `1.22223335`

### Summary

- Arithmetic operations are based on variable actual value (in-memory bits sequence)
- Displaying is based on variable type
- Don't let displaying fool you

## The infamous `0.1 + 0.2 = 0.3`

### With `float` type

```
float a = 0.1f;
float b = 0.2f;
float c = a + b;
double d = (double)a + (double)b;

System.out.println("a = " + (double) a);
System.out.println("b = " + (double) b);
System.out.println("c = " + (double) c);
System.out.println("d = " + (double) d);

---- Output ----
a = 0.10000000149011612
b = 0.20000000298023224
c = 0.30000001192092896
d = 0.30000000447034836
```
We can see with the same float input `a` and `b`, arithmetic operation in `double d` is slightly more accurate than `float c`

### With `double` type

```
double a = 0.1;
double b = 0.2;
double c = a + b;

System.out.println("a = " + (double) a);
System.out.println("b = " + (double) b);
System.out.println("c = " + (double) c);

---- Output ----
a = 0.1
b = 0.2
c = 0.30000000000000004
```
With the double input `a` and `b`, arithmetic operation in `double c` is much more more accurate than above.  
It's because of more accurate input (double vs float)

# Reference

- [Good IEEE 754 summary](https://steve.hollasch.net/cgindex/coding/ieeefloat.html)
- [Short explain storing in mem](https://softwareengineering.stackexchange.com/a/215126)
- [Manually calculate 0.1 + 0.3](https://softwareengineering.stackexchange.com/questions/310623/floating-point-calculation-being-too-exact)
- [Very detail explanation](http://blog.reverberate.org/2014/09/what-every-computer-programmer-should.html)