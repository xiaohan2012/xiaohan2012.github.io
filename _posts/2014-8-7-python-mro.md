---
layout: post
title: "Why MRO in Python 2.2 is bad?"
date:   2014-08-07 20:36:00
categories: note
tags: python mro
---

## Introduction

I just read about how MRO in Python 2.3 is different from that of Python 2.2 [here][mro-article]. It is quite good. Clear, with a lot of illustrative examples.

Here, I summarized why MRO in Python 2.2 is bad. For how MRO in Python 2.3 works, refer to [the article I just mentioned][mro-article]

C3-MRO(Method Resolution Order) is introduced in Python 2.3 to replace the old MRO in Python 2.2.

----------------------------------------------

## What's wrong with MRO in Python 2.2?

Let me use an example from [here][mro-article]: 

    >>> F=type('Food',(),{'remember2buy':'spam'})
    >>> E=type('Eggs',(F,),{'remember2buy':'eggs'})
    >>> G=type('GoodFood',(F,E),{}) #OK in Python 2.2 but raises error in Python 2.3

The inheritance hierarchy is:

                 O
                 |
    (buy spam)   F
                 | \
                 | E   (buy eggs)
                 | /
                 G

          (buy eggs or spam ?)


In Python 2.2, the program runs while in Python 2.3, it does not.


### Error-prone

In Python 2.2, it produces "spam". It is counter-intuitive(therefore error-prone), isn't it? As `E` is more specific than `F`, thus the more specific class, `E`, should be used.


### Ambiguous

For the attribute `remember2buy` of `G`, which class shall be used? `F` or `E`? This is essentially the problem of ambiguity.

----------------------------------------------

## What Python 2.3 does

Python 2.3 wants to force programmer to create good class hierarchies. So it **raises an error** if such case happens. 

For a detailed/general explanation of *what* such cases are. Please refer to [here][mro-article]. 

Also, for how to work out the MRO of a class by hand, refer to [the same article][mro-article]


[mro-article]: https://www.python.org/download/releases/2.3/mro/