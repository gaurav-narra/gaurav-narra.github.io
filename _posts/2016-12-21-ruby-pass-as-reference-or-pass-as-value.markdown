---
layout: post
title:  "Ruby pass as reference or pass as value"
date:   2016-12-21 15:30:35 +0530
categories: methods reference value ruby rails
---

One of the basic misconception while writing a ruby script is assuming that ruby method calling is strictly pass as value. Lets take an example

{% highlight ruby %}
> def do_nothing_method(arg_str)
> arg_str << " & this is also appended"
> arg_str = "But not this"
> end

> str = "This is a string"
> do_nothing_method(str)
> puts str
This is a string & this is also appended
{%endhighlight%}

On calling the method, the original "str" object is getting updated but assing("=") operation is creating a new object. So object id might be passed as value to the method and "<<" is appending the object and "=" is creating a new object, this method calling is almost equivalent to the following

{% highlight ruby %}
> str = "This is a string"
> arg_str = str
> arg_str << " & this is also appended"
> arg_str = "But not this"
> puts str
This is a string & this is also appended
{%endhighlight%}

But in general how does ruby allocate objects? Lets check it with following example, of array of hash's

{%highlight ruby%}
>arr = []
>x = { 1 => 2 }
>arr << x
>y = arr[0]
>y[3] = 4
> x
{1 => 2, 3 => 4}
>arr
[{1=>2, 3=>4}]
{%endhighlight%}

This shows that there was always only one hash object and its reference i.e id, and different variables were pointing to same object. This would be much clear with object_id's

{%highlight ruby%}
> x.object_id
70240176316840
> arr[0].object_id
70240176316840
> y.object_id
70240176316840
{%endhighlight%}

Which is the same, so its the same object being referred from different variables.
Hope this would help you when you have written some thing and is not working the way you expect :)
