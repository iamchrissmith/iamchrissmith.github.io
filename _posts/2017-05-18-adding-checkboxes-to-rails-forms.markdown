---
layout: post
title:  "Rails - Form Checkboxes"
date:   2017-05-18 10:00:00
description: A blog post looking at the checkbox tag in Rails
categories:
- blog
- rails
- ruby
---

Rails makes outputting a form pretty easy. Unfortunately, like with many things at this point in my Rails journey, the magic seems pretty opaque.  One of the things that was confusing in our latest project was adding checkboxes to our forms.

Our goal is to include the following in our form since it is easier for users to checkboxes instead of typing options in manually:

{% highlight html %}
<li>
<input type="checkbox" name="playlist[song_ids][]" id="song-2" value="2">
test song
</li>
{% endhighlight %}

There are two ways to do this:
1) `check_box_tag` and 2) `collection_check_boxes`

So lets see an example of the first.  If we want the HTML output above, we would input:
{% highlight ruby %}
check_box_tag(:song_ids, song.id, false, :name=> 'playlist[song_ids][]', id: "song-#{song.id}")
"test song"
{% endhighlight %}

The first value is the param value and the second is the value.  The third is a boolean for whether the box is checked or not by default.  Our last two are the options (name and id) and their corresponding values.  Since this is a checkbox our user text is separate (you would probably want to wrap that in a label for proper semantics).

If you wanted the checkbox checked by default you would change `false` to `true` after the value.  You can also pass in other HTML options such as `disabled`:

{% highlight ruby %}
check_box_tag(name, value = "1", checked = false, options = {})
{% endhighlight %}

If you had a collection you wanted to make with checkboxes you would need to wrap the first option in a enumerable loop. However, this is where the second method comes to the rescue.

{% highlight ruby %}
collection_check_boxes(:playlist, :song_ids, Song.all, :id, :title)
{% endhighlight %}

This would result (without the enumerable) in the exact HTML we are looking for for each of our songs.  All the options are:

{% highlight ruby %}
collection_check_boxes(object, method, collection, value_method, text_method, options = {}, html_options = {}, &block)
{% endhighlight %}

Some helpful links about these:
- [APIDock-Tag](https://apidock.com/rails/ActionView/Helpers/FormTagHelper/check_box_tag){:target="blank"}
- [APIDock-Collection](https://apidock.com/rails/v4.0.2/ActionView/Helpers/FormOptionsHelper/collection_check_boxes){:target="blank"}
- [Rails Code - Check_box_tag vs Collection_check_boxes](http://www.webdeveloperfromscratch.com/blog/rails-code-check_box_tag-vs-collection_check_boxes){:target="blank"}
