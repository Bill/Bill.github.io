---
author: Bill Burcham
comments: true
date: 2006-09-28 23:11:05+00:00
layout: post
slug: ruby-moduleinclude-at-odds-with-duck-typing
title: Ruby Module#include at Odds with Duck Typing.
wordpress_id: 22
categories:
- Ruby
---

Ruby Module#include is at odds with [Duck Typing](http://en.wikipedia.org/wiki/Duck_typing). Imagine I've got a perfectly good class that produces the numbers 1 thru 5:




I can of course invoke each with a block and print the numbers out:







With predictable results.  But I cannot use a Nums as if it were Enumerable (unless I include Enumerable in the definition).  So this:







Yields "undefined method `collect' for # (NoMethodError) "




Folks who know Ruby will say "well -- you have to include Enumerable".  To which I say "Nums already quacks like an Enumerable doesn't it?"  Or, more accurately -- Nums quacks like something that satisfies the (un-named) interface that an object must implement in order to include Enumerable (to be enumerable).  Nums implements the each method, which is all that Enumerable needs (so long as no one calls the ordering methods on Enumerable).




It just seems kind of wrong that I have to either include Enumerable directly in my class -- or I have to explicitly add the interface after the fact using one of Object#extend (on a Nums instance), Object#instance_eval (on Nums), Module#include or Module#append_features on Nums, or by writing another class decl for Nums to extend it that way.)




The underlying issue here is that there's no [generic functions](http://en.wikipedia.org/wiki/Generic_function) in Ruby.  Topher Cyll has implemented a  [multiple dispatch](http://multi.rubyforge.org/) gem for Ruby that does something similar.  It's not quite generic functions though because dispatch is scoped to a target object (receiver of message) whereas generic functions (in general) pattern match on all parameters -- including message target.




So I got to playing around a little.  I reasoned I could use method_missing to mixin Enumerable and others at runtime like this:







Then I could just "include RespondsToGenerics" in classes I wanted to have this behavior.  It's still ugly that I have to include anything, but hey -- it's a start.  'course what I got then was:




in `bind': bind argument must be an instance of Enumerable (TypeError)




Cuz Ruby won't let me bind an Enumerable method to an Object that doesn't include Enumerable.  Augh!!  In what way is that behavior aligned with the **Zen of Duck Typing**?  I quack like a duck!  Trust me Ruby.  Back to the drawing board.




So to make this work I think I need to create a full-fledged proxy object (that includes all the interfaces in question).  Then I can wrap each object (in method_missing) with the wrapper and the wrapper can forward the message using Object#send.  But hang on, if the wrapper includes all the interfaces in question then I'll have to use Module#undef_method to undef all the methods of the included interfaces so that method_missing gets called.  Double-ugh.  Thinking.  Thinking...




What I really want is for the Nums class definition to be devoid of any code pertaining to (what should be) the generic mixins.  What I really want is to introspect when Nums is defined -- and if Nums implements the required protocol: namely, that Nums implements an each method, then Nums should get "tagged" with the Enumerable interface.  Something like this say:







The idea is that I should be able to explicitly and in one place declare in any mixin module the _requirements_ that module has on classes you might wish to mix it into.  If I could do that then maybe I could make the actual mixing happen automatically -- yielding mixins that are composed environmentally -- not at each point of use.  I really like the fact that this approach reifies the requirements on the target class.  For example you can see that to mix in Enumerable the "each" method must be defined.  It's in code now -- not just buried in the Pick Axe book.




Now implementing mix_in_when is pretty straightforward.  We define it in class Module and we keep track of the predicate passed.  Then some time _later_ we want to do the mixing.  That's where the questions arise.




When should we do the mixing? I was initially tempted to try and redefine Class::new (a class method.)  See, I could just hook creations of instances of class Class and at that point I'd have control and I could evaluate the predicates for the new class and inject the modules as appropriate.  This turned out to be problematic for a couple reasons.




First off, hooking Class::new doesn't work in [all cases](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-talk/23318).  In particular Class::new doesn't get invoked when the class Foo;end syntax is used.  Class::new is invoked only when the Class.new syntax is used.  Then there's the Class::inherited approach which [does get](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-talk/194052) called in all cases but it still wouldn't really be what we want.  Again I hear the crescendo of the Ruby cognescenti.  Ruby class definition is _dynamic_.  The various syntactic forms used to define program elements such as methods, instance variables and the like are mostly method invocations -- evaluated not at compile time as those of us brutalized by long years of C, C++ and Java have been conditioned to expect (bell dings, Bill drools) -- but evaluated at runtime.  As a result, even if you hooked Class::initialize the actual class of interest wouldn't have its methods defined yet.  The method definition code runs in the context of the newly created Class object.  And let's say you could get a hook called after that initial method definition code ran.  You wouldn't be sure even then that the class wasn't going to change on you -- since idiomatic Rubyists are continually modifying other peoples' classes at runtime -- adding methods, adding variables.




Nope, there's no easy way around the fact that you the programmer are going to have to choose when to perform the Big Mixing Act.  For some applications you may even want to do it repeatedly.  So here's what the code actually looks like (I call it auto_mixins.rb):







With that code required in your project you can call Module.AddAutomaticMixins and begin using any conformant object as though it were Enumerable -- without ever including Enumerable in the class yourself.  Similarly for Comparable.  Here's an example:







Run it yourself and see.  Is it something I'm dying to use in my next project?  Well I'm not sure.  My head is still spinning. In a good way.  At this point it's more of a thought experiment.  I'm curious what other folks think about the general idea.



