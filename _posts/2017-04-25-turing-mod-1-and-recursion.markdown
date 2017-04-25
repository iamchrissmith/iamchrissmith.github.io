---
layout: post
title:  "Turing Mod 1 and Recursion"
date:   2017-04-25 15:22:00
description: A quick blog post on Turing Back end - Module 1 and my favorite technical concept, recursion.
categories:
- blog
- turing
- logic
---


I must admit, I had doubts if Turing was the right fit for me. I am a self-taught WordPress developer had been successfully running my own web development company for years. I was worried that I might not get the most out of the program, because I already had a fair amount of coding knowledge. And yet, I was at a point where I wanted to dive deeper into coding and be immersed into a community of similar minded people. So I took the plunge.

## Mod 1 – a test of character
Quickly it became clear, that I had to find my own way of succeeding at Turing. By that I don’t mean just getting the projects done on time but going further to be able to challenge myself and at the same time contribute to the community in the best way I can.

It’s a hard path and a difficult balance to strike. And I am grateful for the support I got from the instructors and the feedback from my cohort and other Turing students. Family and friends who know me well have commented that I look fulfilled and have rediscovered an intrinsic sense of motivation. I take that as a good reality check and as a confirmation that starting the program has so far been a good decision.  

Most importantly though is the feeling that I am looking forward to starting Mod 2.

## Recursion
![Worlds Folding - Dr. Strange Inception](/assets/images/inception.jpg "Inception / Dr. Strange image")
[source](http://www.gizmodo.co.uk/2016/04/first-doctor-strange-trailer-shows-marvel-doing-an-inception/){:target="blank"}

One of the hardest and most fun concept I learned during mod 1 was 'recursion'.  This at times can feel like something out of the move Inception, but it was one of the most powerful tools I learned.  It allows you to loop over a part of your program until a condition is met. Each layer in puts a temporary pause on the layer that spawned it and then ruby cycles back up through the layers completing the remaining program at each level before returning to the level that spawned it.

Here is an example of a recursive method used to implement the [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes){:target="blank"} to find prime numbers:

{% highlight ruby %}
class Sieve
  def initialize(limit)
    @range = (2..limit).to_a
  end

  def primes(start = 2)
    return @range if @range.empty?
    @range.delete_if do |number|
      number != start && number % start == 0
    end
    if start != @range.last
      start = @range[@range.index(start)+1]
      primes(start)
    end
    @range
  end
end

Sieve.new(10).primes
# => [2, 3, 5, 7]
{% endhighlight %}

This creates an array of every number from 2 to the limit (in this case 10) then removes the ones that are multiples of that one then moves on to the next one. The method calls itself (`primes(start)`) with a new starting point in the array until we reach the last number in the array (`if start != @range.last`).  If we were to look at the `@range` array through each step we would see:
```
[2,3,4,5,6,7,8,9,10]
[2,3,5,7,9]
[2,3,5,7]
[2,3,5,7]
```
as it cycled through each level.  However is we insert a `p @range` **after** `primes(start)` (our recursion) is called we would see the following output in the terminal:
```
[2, 3, 5, 7]
[2, 3, 5, 7]
[2, 3, 5, 7]
```
Each time ruby hits the `primes(start)` line we "drop down to a new level of the dream world" (a la Inception).  On that level we execute the `primes` method from the start and if we hit `primes(start)` again, we drop to a level deeper.  We keep doing this until `primes` returns (i.e. until it hits `@range` on the last line).  This returns `@range` back to the previous level which then runs through the rest of its method (in this example printing `@range` to the terminal). It does this for each level until it returns back to the highest level and the program concludes.

Recursion can be very useful for avoiding `while` or `until` loops as well as dealing with smart node navigation (see ["Smart Nodes all the time!"](https://iamchrissmith.io/blog/ruby/logic/2017/04/15/smart-nodes/) for a perspective on smart nodes).

[turing]: https://www.turing.io/
