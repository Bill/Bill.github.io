---
author: Bill Burcham
comments: true
date: 2017-05-27 15:59:16+00:00
layout: post
title: Use Java Lambdas Instead of Method Refs Sometimes
categories:
- Java
- Optional
- Streams
- Functional Programming
---
Java 8 method references are super useful in expressions like these:

{% highlight java %}
Comparator.comparing(
    Person::getLastName,
    String.CASE_INSENSITIVE_ORDER)
{% endhighlight %}

`Comparator.comparing` is a generic method. Here's its signature:

{% highlight java %}
static <T,U> Comparator<T> comparing(Function<? super T,? extends U> keyExtractor,
                                     Comparator<? super U> keyComparator)
{% endhighlight %}

When calling `comparing()`, the first parameter (`keyExtractor`) can be provided
in one of three ways:

1. a method reference (as shown in our example)
2. a reference to a `Function` object
3. a lambda expression

Which alternatives you _may_ use depends on the situation. Depending on the
situation, the compiler will either use the expected return
type[<sup>**[1]**</sup>](#[1]) (of `comparing()`) to determine the type of
`keyExtractor`, or it will use the type of the first parameter to determine the
return type. In the latter case either a method reference or a reference to a
`Function` object is required, since a lambda, in general might not carry
sufficient type information.

Method references are also often seen when `map()`ing, either over streams,
or `Optional`s like:

{% highlight java %}
Optional<Speaker> speaker = Optional.of(new AdmiralStockdale());
System.out.println(speaker.map(Speaker::speak).get());
{% endhighlight %}

On the second line, though, the compiler knows the type of `speaker`, so it
knows the type of the `map()` parameter. Here's `map()`'s signature:

{% highlight java %}
public <U> Optional<U> map(Function<? super T,? extends U> mapper)
{% endhighlight %}

`U` is known from the declaration of `speaker`. The type information carried
by the method reference `Speaker::speak` isn't actually needed in this case.

Not only is it not needed, it represents a minor but annoying maintenance trap.
By explicitly specifying the type of `speaker` and also explicitly specifying
the type of the first argument to `map()` we've set them in opposition when they
needn't be. Sure, the compiler will tell if some future code change causes them
to be incompatible (say if the `speak()` method moves to another class or
interface), but wouldn't it be better if the `map()` expression was decoupled
entirely from the name of that class or interface?

Since the compiler doesn't need that extra type information, and since we pay a
price in maintainability by including the extra type information, it'd be nice
if we had another way to specify the method to `map()`.

How about a trivial lambda:

{% highlight java %}
System.out.println(speaker.map(s->s.speak()).get());
{% endhighlight %}

This code is shorter `s->s.speak()` versus `Speaker::speak`. It also has the
advantage of not tightly coupling the expression to the interface name.
By efficiently leveraging type inference, we've improved our code.

Here's a little Github repo with the Java code:
[Lambda vs Method Reference on Bill's Github](https://github.com/Bill/lambdavsmethodref)

## Summary

In Java 8, functional interfaces, method references, and lambda expressions
provide wonderful new opportunities to create higher-order functions. In some
situations, though, a method reference, by saying too much, presents a little
maintenance trap that can be avoided via a trivial lambda expression.

## Footnotes
<a name="[1]">[1]</a> This is called the "target type" in
[Java type inference parlance](https://docs.oracle.com/javase/tutorial/java/generics/genTypeInference.html).
