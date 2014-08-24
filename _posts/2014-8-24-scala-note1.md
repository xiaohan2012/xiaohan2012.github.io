---
layout: post
title: Tour of scala: learning note(One)
date:  2014-08-24 19:08:00
categories: note
tags: scala
---

I am studying Scala now by starting with [tour-of-scala](http://docs.scala-lang.org/tutorials/tour/tour-of-scala.html)

Here is a summary of the first half of tour.

## Abstract Types

Types can be class members. For example:

      class MyClass{
      	    type T
	    val elems <: List[T]
      }

Where  `elems` is subtype of `List[T]`.


## Annotations

Just like the annotaiton in Java:

- scala.serializable
- scala.deprecated

Java annotations can be used in Scala.

Parameters(even optional parameter) can be passed in. The syntax between Java and Scala is slightly different.

## Classes

Methods inheritted from ancestors need should be marked with `override` if overriding the super one, for example, `toString`.

## Case classes

Useful for:

- automatic class attribute setting through class initialization
- recursive decomposition mechanism via pattern matching.

## Compound types

Use the `with` keyword to combine for example `trait`s.

And it can be used to require the type of function parameter to have certain trais. For example:

    def cloneAndReset(obj: Cloneable with Resetable): Cloneable = {...}

## Sequence Comprehension

Syntax:

	for(v <- expr [if condition])  {
	      yield expr
	}

Iteration can be nested.

	for(v1 <- expr1 [if condition];
	    v2 <- expr2 [if condition])  {
	      yield expr
	}

`Unit` can be returned by omitting the `yield` keyword.

## Extractor Objects

Case classes provide built-in packing and unpacking. Without case clasess, these can also be done by using `apply` and `unapply`.

Function/method can return something or nothing by using `Option[T]`.

`Some` and `None` are subtypes of `Option[T]`.

## Generic classes

Class like this:
       class Stack[T] {
              ...
       }      

`T` is a type parameter. It is invariant(as opposed to covariant/contravariant).

## Implicit parameters

If not argument given, implicit parameter takes effect and only objects/methods that are marked implicit can be used to fill in the gap.

## Inner classes

Similar to Java's inner class, one class can be defined in another. 

	class Graph {
		class Node {
		}
	}

	val g1 = new G
	val g2 = new G

	g1.Node == g2.Node //false!

In order to refer to the same `Node`, use `Graph#Node`.

## Mixin Class Composition
How scala implements multiple inheritance ---- through `trait`s.

    class Iter extends StringIterator(args(0)) with RichIterator


We can even define class like this? `StringIterator(args(0))`

- `StringIterator` is the super class
- `RichIterator` is the mixin


## Nested Functions

Function definition can be nested. The inner function forms a closure.

## Anonymous Function Syntax

   (x: Int) => x + 1

is equal to 

    new Function1[Int, Int] {
    	def apply(x: Int): Int = x + 1
    }


Note: function in Scala is also object

## Currying

To use currying, define function like this:

   def func(arg1)(arg2)..(argn) = {...}

## Automatic Type-Dependent Closure Construction

This is just a more advanced name of *call-by-name*. *call by name*, in contrast with *call by value*, evaluates the parameter when it is actually used in the function body.

This feature can be used to create syntax like *while*, *do-until*.