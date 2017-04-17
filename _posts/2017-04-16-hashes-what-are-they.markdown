---
layout: post
title:  "Ruby Hashes... what are they good for?"
date:   2017-04-16 15:11:45
description: A post exploring ruby hashes, what they are and some of what you can do with them.
categories:
- blog
- ruby
permalink: ruby-hashes-what-are-they
---

Hashes are a very powerful and useful tool in Ruby, so I wanted to dig a little more into them.

One of the most commonly used data storage structures in my programming so far has been Arrays.  Arrays are great, they are lists of things that you might need later.  You can access them fairly easily using using the index (i.e. `my_awesome_array[0]`).  In Ruby, there are powerful built in enumerables that allow you to look into and manipulate arrays. Arrays however are not great at relating data to other data so can be cumbersome for some types of data or flows.

Other programming languages (PHP and Javascript in my experience) also have something like hashes though they are call associative arrays.  This is a helpful naming convention in my opinion since hashes and arrays operate similarly with their key difference being that one associates data together.

So what does that mean.  Basically if we have array: `[1,2,3,4]`, there is no relationship between your data. Unless you know the order of the array, you can get from `1` to `2`.  However in a hash `{a: 1, b: 2}` you can only know `a` get back `1` since those data are associated with each other and a `key`/`value` pair.

This can make working with data faster and easier.  Faster because you don't have to iterate through an entire array to find the `value` you are looking for and easier because the data starts to have meaning beyond its order. In [one Stack Overflow post](https://stackoverflow.com/questions/5551168/performance-of-arrays-and-hashes-in-ruby){:target="blank"} their tests show that searching an array vs finding a key in a hash resulted in a more than 2 sec difference in time when searching 10,000 entries.

So how can a hash be so fast compared to an array.  As I am learning with Ruby, EVERYTHING is an object.  After a bit of digging, I found out that a Hash is actually 1) an object and 2) storing its information in `bin`s.  These `bin`s are actually... drumroll please... *arrays*!

So here is what I understand is happening: When you create a new `Hash` ruby instantiates a new Hash object.  This object starts with 11 `bin`s (Arrays).  If any `bin` has more than 5 elements in it, ruby reorganizes things and increases the number of `bin`s.  This process can be a bit slow but only has to happen once. When you are looking for something in the `Hash` ruby is smart enough to calculate its `bin` again and only look inside that `array`.  This sounds an awful like a search tree algorithm which is why we go from a O(n) for array searches to O(1) for hash search times.

Further Reading:
- [Hash Lookup from Engine Yard](https://blog.engineyard.com/2013/hash-lookup-in-ruby-why-is-it-so-fast){:target="blank"}
- [How the hash works](https://launchschool.com/blog/how-the-hash-works-in-ruby){:target="blank"}
- [Ruby & Big O](http://blog.honeybadger.io/a-rubyist-s-guide-to-big-o-notation/){:target="blank"}
- [How Ruby Uses Memory](https://www.sitepoint.com/ruby-uses-memory/){:target="blank"}
- [How Ruby Uses Memory](https://www.sitepoint.com/ruby-uses-memory/){:target="blank"}
