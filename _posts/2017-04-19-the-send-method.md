---
layout: post
title:  "Ruby's send method"
date:   2017-04-19 09:50:00
description: A post exploring Ruby's send method and some uses.
categories:
- blog
- ruby
permalink: ruby-send-method-exploration
---

A pattern I see recurring in my programming is that I need to take an argument, then based on what it is do something, for instance:
{% highlight ruby %}
def my_method(array_of_args, action)
  if action == :sum
    my_math.sum(array_of_args)
  elsif action == :average
    my_math.average(array_of_args)
  end
end
{% endhighlight %}

So in this example, we are taking two arguments, one is an array of numbers, the second is a key that represents the action we want to perform on the array.

I recently discovered a new way of doing this that makes it much simpler.  So if we assume that the actions we are passing in are the same name as the methods we have on our object we can do this:
{% highlight ruby %}
def my_method(array_of_args, action)
  my_math.send action array_of_args
  # my_math.send(action, array_of_args)
end
{% endhighlight %}

So `.send` takes a symbol (or a string) for its first argument which corresponds to the method name.  The next n arguments are the arguments that get passed into your method.

One interesting thing I found out about the send method from [Stack Overflow](http://stackoverflow.com/a/3337954/6761672){:target="blank"} is that it will bypass "visibility checks" so it gets around the `private` designation on methods and could be used to directly test those methods.

This is a little slower than directly calling the method directly (i.e. `class.method_name`) according to [Khelll's Blog](http://blog.khd.me/ruby/ruby-dynamic-method-calling/){:target="blank"}

Further Reading:
- [Metaprogramming in Ruby](http://ruby-metaprogramming.rubylearning.com/html/ruby_metaprogramming_2.html){:target="blank"}
- [What does send() do in Ruby](https://stackoverflow.com/questions/3337285/what-does-send-do-in-ruby){:target="blank"}
- [Ruby dynamic method calling](http://blog.khd.me/ruby/ruby-dynamic-method-calling/){:target="blank"}
