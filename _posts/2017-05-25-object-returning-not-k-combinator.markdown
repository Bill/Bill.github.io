---
author: Bill Burcham
comments: true
date: 2017-05-25 13:57:46+00:00
layout: post
title: Object#returning Is Not a K-Combinator
categories:
- Ruby
- Functional Programming
- Clojure
- Haskell
- Combinatory Logic
- Rails
---
Rails adds a useful method to `Object`: `returning`. Here's the source code:

{% highlight ruby %}
# A Ruby-ized realization of the K combinator, courtesy of Mikael Brockman.
#
#   def foo
#     returning values = [] do
#       values << 'bar'
#       values << 'baz'
#     end
#   end
#
#   foo # => ['bar', 'baz']
#
#   def foo
#     returning [] do |values|
#       values << 'bar'
#       values << 'baz'
#     end
#   end
#
#   foo # => ['bar', 'baz']
#
def returning(value)
  yield(value)
  value
end
{% endhighlight %}

Pass `returning` an object and a block and it returns the object, after running the block on that object.

Clojure has a similar function called [`doto`](https://clojuredocs.org/clojure.core/doto) that generalizes the idea. Here's an example:

{% highlight clojure %}
;; Note that even though println returns nil, doto still returns the HashMap object
user> (doto (java.util.HashMap.)
            (.put "a" 1)
            (.put "b" 2)
            (println))
#<HashMap {b=2, a=1}>
{"b" 2, "a" 1}
{% endhighlight %}

The comment in the Ruby on Rails code and Jamis Buck's, 2006 [Mining ActiveSupport: `Object#returning`](http://weblog.jamisbuck.org/2006/10/27/mining-activesupport-object-returning) refer to this functionality as an example of the K combinator. Though `returning` and `doto` are useful, and they can really improve your (object-oriented, side-effect-dependent) code by making your intentions plain, they are not examples of the K combinator.

Contrary to Buck's analysis, a K combinator is not a "function of two arguments". Well, in a curried language like Haskell, it _is_ a function of two arguments. But the way that two-argument function is always used, is as a one-argument function that produces a function. Dig Haskell's [`const`](http://hackage.haskell.org/package/base-4.9.1.0/docs/Prelude.html#v:const), an actual K combinator:

{% highlight haskell %}
const :: a -> b -> a
{% endhighlight %}

Indeed you see a two argument function. But the way `const` will be used is:

{% highlight haskell %}
>>> map (const 42) [0..3]
[42,42,42,42]
{% endhighlight %}

See what happened there? `const 42` produces a function, which, when passed any parameter, e.g. 0, 1, 2, or 3, will just return 42.

Similarly, Clojure has [`(constantly x)`](https://clojuredocs.org/clojure.core/constantly) that:

> Returns a function that takes any number of arguments and returns x.

These are K combinators.

`Object#returning` and `doto` are certainly higher-order functions (`doto` actually happens to be a macro) in that they take a "function" as a parameter. In the case of `Object#returning` the "function" is a block. `doto` is a bit more general in that it takes one or more forms, not merely functions.

But neither `Object#returning` nor `doto`, in general, _return_ a function. Contrast this with `const` and `constantly`, both of which return functions.

## Summary

Ruby on Rails' `Object#returning` and Clojure's `doto` are useful for clearly delineating code whose only purpose is to introduce side-effects on or about some target object. Examples include:

* debug logging
* multi-step object initialization through setters

These are higher-order functions in that they each take a functional argument (or in `doto`'s case, one or more). Neither, however, in general, produces a function.

The K combinator, as exemplified by Haskell's `const` and Clojure's `constantly`, is useful in cases where you need to succinctly construct a function that, irrespective of its argument(s), always returns the same value. A K combinator is a higher-order function that _produces_ a function.

`Object#returning` and `doto` are not K combinators. `const` and `constantly` are. These are two useful, but distinct, concepts.
