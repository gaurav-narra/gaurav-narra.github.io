---
layout: post
title:  "Ruby attribute accessors"
date:   2016-08-28 8:48:35 +0530
categories: attr_accessor ruby rails
---

I tend to use a lot of attribute accessors to my ruby class's. They basically add getter and setter methods to my class, for example suppose we have a `Snake` class, with public method grow, shrink & reputation. Where grow would increase the length of snake by a unit, shrink would decrease lenght by a unit & reputation uses length to calculate snake's reputation. Here the `length` is an attribute which would be referred as well as updated within the class.Then my class would be as following:

{% highlight ruby %}
  class Snake

    attr_accessor :length

    def initialize(params)
      self.length = params['length']
    end

    def grow
      self.length += 1
    end

    def shrink
      self.length -=1
    end

    def reputation
      (length)*(1.45)*(...)
    end

  endri
{%endhighlight%}

When I define `attr_accessor :length` it would correspondingly generate the following setter & getter methods:

{% highlight ruby %}
  class Snake

    def length
      @length
    end

    def length=(var)
      @length = var
    end

  end
{%endhighlight%}

As you might have noticed I am calling the setter method explicitly using `self` but for getter, calling implicitly  also works. When you try to do an implicit call with setter method, `length = 10` then you are actually creating a variable `length` instead of calling the setter method which updates the instance variable `@length`. For example:

{% highlight ruby %}

  :001 > @length = 20
  => 20
  :002 > self.length = 30
  => 30
  :003 > @length
  => 30
  :004 > length = 20
  => 20
  :005 > @length
  => 30

{%endhighlight%}

And also when you create an instance of Snake object, `length` attribute would be public, so any other class can update the length of the snake. This leads to weak encapsulation, to avoid it and allow only grow & shrink methods to update snakes length we can create private attr_accessor, ie.,

{% highlight ruby %}
  class Snake

    private
    attr_accessor :length

    public
    ...
  end

{%endhighlight%}


But the obvious logical question which arises is, by definition we cannot call private methods explicitly. So when we try to call setter method, `self.length = 10` then it should be throwing an error saying private method length is called. But it does not, apparently ruby allows us to call setter private methods explicitly, which actually make's sense, else we would not be able to write private setter methods.


{% include comments.html %}
