---
layout: post
title:  "Smart Nodes all the time!"
date:   2017-04-15 16:00:00
description: A quick blog posts about the wonder of smart nodes
categories:
- blog
- ruby
- logic
---

With Ruby, one of the topics/strategies that keeps coming up is the idea of pushing logic as far down the object tree as possible.

If you have an object tree in your program such as:
```
            /---- Child Node
Parent ----/
```
The parent is probably creating the child node to store information or in the case of some sort of linked list, to reference other nodes.  When this happens I have found it to be extremely useful to use the child node as much as possible to perform logic and information processing.  The parent will then just be calling methods on the child to get information.

For one of our projects at [Turing][turing] we needed to traverse a [Trei (Search tree)](https://en.wikipedia.org/wiki/Trie).  Instead of having the parent look through each of its children and grand children and grand grand children, I had the parent ask its `@head` if it had the necessary information, then that same method on the `@head` was asked the same question and on down the chain.  Using recursive methods for this type of search allowed the results to roll back up the chain with all the info I was looking for.

In another project, we were recreating the game Battleship.  While a straightforward way of storing the board was an array of arrays:
{% highlight ruby %}
class Board
  def initialize
    @board = [
      ["A1", "A2", "A3", "A4"],
      ["B1", "B2", "B3", "B4"],
      [etc...]
    ]
  end
end
{% endhighlight %}
I used smart squares on the board.  So instead of my board (`parent`) knowing about the whole board, it only knew about `A1`.  `A1` knew about `A2` and `B1` and so on and so forth (each knew its up, down, left, and right neighbors).  
{% highlight ruby %}
class Board
  def initialize
    @head = <Square @row="A" @column="1"... >
  end
end

class Square
  def initialize
    ...
    @neighbors = {
      above: nil,
      below: <Square @row="B" @column="1"... >,
      left: nil,
      right: <Square @row="A" @column="2"... >
    }
  end
  ...
end
{% endhighlight %}

While creating this board the first time was somewhat complicated, it changed my AI's move method from ~15 lines to two:

From:
{% highlight ruby %}
def move(current, direction)
  case direction
  when "up"
   rows = our_rows
   index = our_rows.index(current[0])
   [rows[index - 1], current[1]]
  when "down"
   rows = our_rows
   index = our_rows.index(current[0])
   [rows[index + 1], current[1]]
  when "left"
   [current[0], (current[1].to_i - 1).to_s]
  when "right"
   [current[0], (current[1].to_i + 1).to_s]
  end
end
{% endhighlight %}
To:
{% highlight ruby %}
def move(current, direction)
  current_square = translate_location(current)
  next_square = current_square.neighbors[direction.to_sym]
  "#{next_square.row}#{next_square.column}"
end
{% endhighlight %}

[turing]: https://www.turing.io/
