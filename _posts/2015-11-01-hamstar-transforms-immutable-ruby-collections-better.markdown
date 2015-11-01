---
author: Bill Burcham
comments: true
date: 2015-11-01 05:44:41+00:00
layout: post
title: Hamstar Transforms Immutable Ruby Collections Better
categories:
- Ruby
- Functional Programming
- AWS
- Clojure
---
When working with Amazon's [AWS SDK for Ruby](http://docs.aws.amazon.com/sdkforruby/api/index.html) there are two flavors of API. The most mature parts of AWS have what's called a [resource interface](http://docs.aws.amazon.com/sdkforruby/api/index.html#Resource_Interfaces__aws-sdk-resources_gem_). These are fine-grained object-oriented interfaces to AWS objects. Want to delete a bunch of objects having the `/tmp-files/` path in an S3 bucket? Easy:

{% highlight ruby %}
bucket.objects(prefix: '/tmp-files/').delete
{% endhighlight %}

That's a convenient way to deal with S3 buckets. Alas vast swaths of the AWS service-space have no such convenient interface. Those services offer only a thin veneer over the underlying REST interface via a client object. A prime example of a service offering this more primitive interface is the [EC2 Container Service](http://docs.aws.amazon.com/sdkforruby/api/Aws/ECS.html) or ECS.

If you want to say, change the version of a Docker image, referenced within an ECS task definition, the process is:

1. call `describe_task_definition` to retrieve the structure
2. pluck the `container_definitions` array from the structure and modify the `image` value for the `container` of interest
3. call `register_task_definition`, passing the doctored container array

Ruby is pretty good at this sort of thing. At step 2, you just mutate the structure retrieved in step 1. At step 3 you submit part of the mutated structure back to AWS.

If you've spent any time programming in Haskell or Clojure though, you may be hankering for a more "functional" solution. Do you really have to mutate that big complex structure? What if later you found that you needed two different versions of the structure, with two different mutations? Would you retrieve the structure from AWS twice? Or would you think about "deep cloning" the structure each time? That way lies madness.

First, we'll take a look at how we would usually approach this in Ruby, then we'll look at how a Clojurist might think about the problem, and finally we'll apply a more functional appproach to our Ruby solution. In the process we may even do Clojure one better!

## The Problem

The AWS ECS client returns a structure something like this from `describe_task_definition`:

{% highlight ruby %}

response = {
  task_definition: {
    task_definition_arn: 'some-arn-string',
    container_definitions: [
      {
        name: 'front',
        image: 'nginx:1.7'
      },
      {
        name: 'my-python-web-app',
        image: 'my-python-web-app:latest'
      }
    ]
  }
}
{% endhighlight %}


Now there is a lot more stucture in the actual response. I'm just showing the parts that are essential for our discussion.

What I'd like to do is upgrade the NGINX Docker image used by the container named "front", to version 1.9. Note that in my upgrade script, I don't know what version of nginx is currently in place, nor do I want to upgrade every reference to the NGINX image. I only want to upgrade 'front' to NGINX version 1.9.

Here's some typical Ruby to do the job:

{% highlight ruby %}

response[:task_definition][:container_definitions].each { |c| \
  c[:image] = 'nginx:1.9' if c[:name] == 'front' }
{% endhighlight %}


Now our response object can be submitted back to the server. But what if we didn't want to mutate the original (deep) structure? Let's just do a plain old `clone` of that object and see what we get:

{% highlight ruby %}

> r2 = response.clone
> response.__id__
 => 2155921900
> r2.__id__
 => 2161504860
{% endhighlight %}


So far, so good: we've made a copy of the response object! But wait:

{% highlight ruby %}

> response[:task_definition].__id__
 => 2155921940
> r2[:task_definition].__id__
 => 2155921940
{% endhighlight %}


While the top-level Hash has been cloned, the internal values have not. This was not a deep clone.

You may want to take a moment now to follow the rabbit down the hole of deep cloning Ruby objects. Or you may want to go shave your yak. I've been down that hole and I've spent time with the yak and I believe there is a better way.

## Forget Deep Cloning: Use Hamster's Immutable Collections Instead

When we program in Clojure we never clone anything. Why is that? Well its because the day-to-day operations we perform on collections always automatically create what appear to be brand new, modified, copies. Part of the magic of Clojure is that this illusion is efficiently maintained. Hint: your structures aren't really deep cloned every time you would otherwise mutate them. The [Clojure Data Structures page](http://clojure.org/data_structures) explains it well.

Can we use the same idea to completely side-step this problem in Ruby? Well yes, we can! Some smart people already created a Clojure-like immutable collections library for Ruby. The [Hamster Gem](https://github.com/hamstergem/hamster) gives you a set of collection classes that mirror the familiar Ruby ones like Hash and Array. But the Hamster classes provide immutable operations ([Hamster API documentation is here](http://www.rubydoc.info/github/hamstergem/hamster/master)).

Here's the Hamster code:

{% highlight ruby %}

require 'hamster'
containers = Hamster.from(response)[:task_definition][:container_definitions].\
    map{|c| c[:name] == 'front' ? c.put( :image, 'nginx:1.9') : c}
=> Hamster::Vector[Hamster::Hash[:name => "front", :image => "nginx:1.9"], Hamster::Hash[:name => "my-python-web-app", :image => "my-python-web-app:latest"]]
{% endhighlight %}


We convert the response to immutable collections and then we use map to _efficiently_ create a brand new modified `container_definitions` array (`Vector`). The new structure will share the unmodified parts of the original. Only the modified parts will require extra storage.

This is fine if we only need to modify one container. But what if we wish to retain the deep structure? The Hamster example just given isn't actually doing everything our original Ruby example did. This first Hamster example is only creating a new `containers` vector. What about the enclosing `task_definition` hash?

It is not necessarily obvious, that immutable versions of Hash and Array will be sufficient for this task. The `response` structure is a Hash containing a Hash containing an Array of Hashes. Will Hamster provide a way for us to modify these deep structures in nested containers and create new nested containers (or at least give us that illusion)?

The answer is "yes!". [Hamster provides an `update_in` method](https://github.com/hamstergem/hamster#transformations) that works just like [Clojure's `update-in` function](http://clojuredocs.org/clojure.core/update-in). It's accessed as a method on Hamster Hash and Vector and it is generic enough to transform deep structures consisting of Hashes and Vectors.

Hamster `update_in` takes a path specification of "keys" (hash keys and array indices) into your deep (hash and array) structure and applies a block to the target value, deep within the structure. Then as it unwinds the stack it creates you an efficient copy of the structure: only the modified parts are actually duplicated.

With `update_in` you could, for instance, update the `:image` on container 0 to `nginx:1.9` like this:

{% highlight ruby %}

response_v2 = Hamster.from(response).update_in( :task_definition, \
  :container_definitions, 0, :image){|i| 'nginx:1.9'.tap{|i2| \
    puts "changing #{i} to '#{i2}'"}}
=> Hamster::Hash[:task_definition => Hamster::Hash[:task_definition_arn => "some-arn-string", :container_definitions => Hamster::Vector[Hamster::Hash[:name => "front", :image => "nginx:1.9"], Hamster::Hash[:name => "my-python-web-app", :image => "my-python-web-app:latest"]]]]
{% endhighlight %}


You can see the Clojure philosophy at work here. An array or vector is very much like a hash in that an array maps keys to values. In the case of an array or vector the keys all happen to be natural numbers. Hamster has stacked the deck by providing a core set of methods on both `Hamster::Vector` and `Hamster::Hash`, that `update_in` can rely on, specifically `fetch(key,default)` and `put(key,val)`. We Rubyists call this duck typing. Hamster's `update_in` is described in the [*Transformations* section of the Hamster API doc](http://www.rubydoc.info/github/hamstergem/hamster/master#Transformations). Hamster defines [a mix-in module called `Associable`](http://www.rubydoc.info/github/hamstergem/hamster/master/Hamster/Associable) that implements `update_in()` for classes that meet the criteria.

## One More Mile To Go&mdash;Stay Strong

This would be great if we always knew the index of the Vector element that we wanted to update. But in general we do not. What we really want to do is update whatever Vector element is a Hash with `:name` equal to `'front'`. Instead of specifying Vector offset '0' we'd like to specify the element whose _value_ meets our criterion.

What if we had a function called `update_having` that could take a different kind of path specification e.g.

update_having: :task_definition, :container_definitions, [:name,'front'], :image
update_in:     :task_definition, :container_definitions, 0,               :image

I've created [a little gem called Hamstar](https://rubygems.org/gems/hamstar) that does just that. Dig:

{% highlight ruby %}

require 'hamstar'
response_v3 = Hamstar.update_having( Hamster.from(response), \
 :task_definition, :container_definitions, [:name,'front'], :image){|i| 'nginx:1.9'}
 => Hamster::Hash[:task_definition => Hamster::Hash[:container_definitions => Hamster::Vector[Hamster::Hash[:name => "front", :image => "nginx:1.9"], Hamster::Hash[:name => "my-python-web-app", :image => "my-python-web-app:latest"]], :task_definition_arn => "some-arn-string"]]
{% endhighlight %}


Rather than injecting `update_having()` into Hamster classes (as a method) I opted to implement it as a (`module_function`) on the Hamstar module. This is less intrusive and might make it a little more apparent that `update_having()` operates on compound structures of arrays and vectors.

The name "Hamstar" is a portmonteau of "Hamster" and "star". I chose the name because of the other feature Hamstar adds, and that is the [Kleene star](https://en.wikipedia.org/wiki/Kleene_star). You can use the Kleene star to match all container elements.

Say you wanted to capitalize all the names of the containers. You could:

{% highlight ruby %}

response_v4 = Hamstar.update_having( Hamster.from(response), \
 '*', :container_definitions, '*', :name){|n| n.capitalize}
=> Hamster::Hash[:task_definition => Hamster::Hash[:container_definitions => Hamster::Vector[Hamster::Hash[:image => "nginx:1.7", :name => "Front"], Hamster::Hash[:image => "my-python-web-app:latest", :name => "My-python-web-app"]], :task_definition_arn => "some-arn-string"]]
{% endhighlight %}


The addition of the Kleene star was inspired by the [Instar Clojure library](https://github.com/boxed/instar). That's an interesting and powerful library. It doesn't, however, provide the associative selection that [Hamstar](https://github.com/Bill/hamstar) offers.
