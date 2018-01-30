##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/assignment-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/assignment-methods-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Assignment methods in ruby

In this article, we'll look at what assignment methods are and how they work.

----------
This article is divided into the following sections:

 1. [What are assignment methods in ruby?](#)
 2. [Assignment methods always return the assigned value](#)
 3. [Summary](#summary)
----------
## What are assignment methods in ruby?
The methods in which we use the suffix equals (=) are called assignment methods. The suffix indicates that we can use the method to change the state of the object and moreover, we can do this using the assignment syntax. For instance, there are such methods for strings, arrays and hashes.

Examples of how they work:

**Array#[]=**

	a = ["first element", "second element", "third element"]
	a[0] = "new element"
	a
produces:

	=> ["new element", "second element", "third element"]   

**Hash#[]=**

	a = {"key": "value"}
	a["new_key"] = "new value"
	a
produces:

	=> {:key=>"value", "new_key"=>"new value"}   

**String#[]=**

	a = "new string 1"
	a[-1] = "2"
	a

produces:

	=> "new string 2"    

From the point of view of the classes created by the programmer, the most common example is the setter method in which the suffix "=" should be used.

Example:

	class Tower

	  def color
	    @color
	  end

	  def color=(color)
	    @color = color
	  end

	end

	tower = Tower.new
	tower.color = "green"
	puts tower.color
	tower.color = "yellow"
	puts tower.color

produces:

	green
	yellow
	=> nil  


----------
## Assignment methods always return the assigned value
Let's take a look at what assignment methods are usually returned. Typically, methods return the result of the last expression or if we specify explicit return from the method the method will return to us something else. In the case of assignment methods, we have a well-defined value, it will return only the assigned value.

Let's define a method with an explicit return and try to return something else from the method. To do this, we will use the class from the previous example and change it a bit:

	class Tower

	  def color
	    @color
	  end

	  def color=(color)
	    @color = color
	    return "string string string"
	  end

	end

	tower = Tower.new
	tower.color = "green"

produces:

	=> "green"  

Hm.. What can I say? It's Amazing!

Let's go a little further. What if the method is empty inside? Do we still get the passed value as the return value?

Our new example:

	class Tower

	  def color
	    @color
	  end

	  def color=(color)
	  end

	end

	tower = Tower.new
	tower.color = "green"

produces:

	=> "green"   
This is the reason why you should avoid situations where you use assignment methods in control structures, you can get non-obvious results.


----------
## Summary
- The methods in which we use the suffix equals (=) are called assignment methods;
- Assignment methods always return the assigned value, even if method is empty inside.


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/assignment-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/assignment-methods-in-ruby</a>
