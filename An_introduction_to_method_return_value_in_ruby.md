##### Last version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-method-return-value-in-ruby" target="_blank">https://www.alexindev.com/posts/an-introduction-to-method-return-value-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## An introduction to method return value in ruby

In this article, we will consider the general introductory moments of how ruby determines the return value for a method.

----------
This article is divided into the following sections:

 1. [Why it's important to know how ruby determines a return value](#mpty-return-returns-nil)
 2. [A method always returns only one value](#e-can-implicitly-define-an-array)
 3. [A return value and the work of the puts method - different things](#he-rxecuted)
 4. [Types of return from a method](#he-rterrupt-the-call-stack)
 5. [Exception from all rules about return value - assignment methods](#hasfasfasfas)
 6. [A return value can be directly assigned to variables](#dasdsad)
 7. [Conclusions](#conclusions)

----------
## Why it's important to know how ruby determines a return value
Why is it important? For instance, we have a method and we want to assign a result of a method to a particular variable. To make sure that we assign exactly what we want, we need to know what value a method will return.

Let's write a few methods in which we specify an explicit return:

	def first_var_of_sum(a,b,c)
	  return a + b + c
	  a + b
	  c
	end

	def second_var_of_sum(a,b,c)
	  return
	  a + b + c
	  a + b
	  c
	end

	def third_var_of_sum(a,b,c)
	  a + b + c
	  a + b
	  c
	end

	a = first_var_of_sum(1,2,3)
	b = second_var_of_sum(1,2,3)
	c = third_var_of_sum(1,2,3)

	puts a.inspect
	puts b.inspect
	puts c.inspect

Description of the code:

 1. We defined 3 methods. If you look closely, you can see that they
    perform a similar set of expressions, but there is one exception, in
    some methods the keyword return is used that affects a return value;
 2. Next, we use the inspect method to see what value the local variable refers to.


The result of the code execution:

	6
	nil
	3
	=> nil

As we see, each variable has a different value. How did this happen? In the first_var_of_sum method, the return keyword is defined before the very first expression, which means that the method evaluates the expression a + b + c (in our case it will be 6) and then returns this object to us.

In the second_var_of_sum method, the return keyword is defined before all other expressions that are in the method. Thus, this method will always return nil.

In the third_var_of_sum method, we didn't use the return keyword, and hence the method computed all 3 lines of code, but as the return value, the method returns only the result of the last expression. In our case, the local variable 'c' will refer to the value 3.

Thus, depending on whether we use the explicit return from the method or not, a method with a similar set of expressions can work in the fundamentally different way. And it is better to understand such nuances than to guess where some strange values came from. We will discuss these details in this and following articles.


----------
## A method always returns only one value

A method in ruby can return only one object. It's difficult to imagine how we would have to work with methods if they could return five or six values at once. The essence of creating methods is partly that a method can return different values depending on parameters, but for the same parameters, a method should return the same value.

However, it should be taken into account that as one value can go an array in which you can put all the objects that you want to return from a method. We will see how to do this a little bit later. At the moment it's important to understand that it will always be one object.


----------
## A return value and the work of the puts method - different things
As we said earlier, a method has only one return value. However, in various examples, you can see a lot of lines in the console.

For instance, it might look like this:

	Firt argument
	Second argument
	Third argument
	=> nil  

We see 3 lines and then goes '=> nil' What does this mean?

The first 3 lines, in this case, are the result of the behavior of the puts method, the meaning of which is that if we execute code, we can get information from the code in the console, the last line denotes the return value. In other words, the return value is nil.

For instance:

	def print_arguments(first, second, third)
	  puts first
	  puts second
	  puts third
	  return
	end

	a = print_arguments("Firt argument", "Second argument", "Third argument")
	puts "Value of local variable 'a': #{a.inspect}"

produces:

	Firt argument
	Second argument
	Third argument
	Value of local variable 'a': nil
	=> nil  

As we can see, the local variable was assigned the value nil, the same nil that we saw as "=> nil". Thus, not everything that we see in the console is a return value, it's important to pay attention to what the hash rocket ('=>') in the console indicates, this value will be the return value.


----------
## Types of return from a method
In ruby, there are 2 types of return from the method: explicit return and implicit return.

An example of the explicit return from a method:

	def some_method
	  puts "Hello! I'm some_method"
	  return "What is your name?"
	end

	some_method

produces:

	Hello! I'm some_method
	=> "What is your name?"  
As we see, the explicit return from a method is a return caused by the keyword return.

An example of the implicit return from a method:

	def some_method
	  puts "Hello! I'm some_method"
	  "What is your name?"
	end

produces:

	Hello! I'm some_method
	=> "What is your name?"  

The implicit return from a method is a return that occurs by default, without using the keyword return. In following articles, we will discuss how they are used and how to work with them in more detail.


----------
## Exception from all rules about return value - assignment methods

Since we haven't yet considered how the explicit and implicit return from the methods works, it's possible that this part of the article is placed here a little earlier than necessary. At this point, just remember that the assignment methods have their own rules.

Previously, in the article about assignment methods, these features were already mentioned. You can see it here: https://www.alexindev.com/posts/assignment-methods-in-ruby#always-return-the-assigned-value

Let's take a look again at how it works. For the assignment method doesn't matter what is defined inside it. The assignment method will always return a value that we passed to it.

Let's see all 3 examples. An example when the assignment method doesn't have expressions inside itself (usually returns nil):


	class Tower

	  def usual_method
	  end

	  def color=(color)
	  end

	end

	tower = Tower.new
	a = tower.color = "green"
	puts a
	puts "--------------"
	b = tower.usual_method
	puts b


produces:

	green
	--------------

	=> nil   

An example with the implicit return (usually a result of the last line of a method is returned):

	class Tower

	  def usual_method
	    "last " + "expression"
	  end

	  def color=(color)
	    "last " + "expression"
	  end

	end

	tower = Tower.new
	a = tower.color = "green"
	puts a
	puts "--------------"
	b = tower.usual_method
	puts b

produces:

	green
	--------------
	last expression
	=> nil   

An example with the explicit return from the method (usually an object for which the return keyword is used is returned):

	class Tower

	  def usual_method
	    return "last " + "expression"
	  end

	  def color=(color)
	    return "color color color"
	  end

	end

	tower = Tower.new
	a = tower.color = "green"
	puts a
	puts "--------------"
	b = tower.usual_method
	puts b

produces:

	green
	--------------
	last expression
	=> nil   

Thus, when you see an assignment method - you can immediately forget about most of the rules that concern the return value in ruby.


----------
## A return value can be directly assigned to variables

Quite often in a code you can see something like:

	a = method_name_one
	b = method_name_two

As we see, we don't specify any specific values, instead, ruby allows us to specify a method and assign its return value to a variable directly.

Example:

	def method_one
	  1 + 1 + 1
	end

	def method_two
	  2 + 2 + 2
	end

	a = method_one
	b = method_two

	puts a
	puts b
produces:

	3
	6
	=> nil   

We can also use multiple assignment. To do this, a method must return an array of values.

Example:

	def method
	  ["value 1", "value 2", "value 3"]
	end

	a = method

	puts a

produces:

	value 1
	value 2
	value 3
	=> nil  


----------
Conclusions
- depending on whether we use the explicit return from a method or not, a method with a similar set of expressions can work in fundamentally different way;
- a method always returns only one value;
- a return value and the work of the puts method - different things;
- in ruby, there are 2 types of return from the method: explicit return (using the return keyword) and implicit return;
- exception from all rules about return value - assignment methods;
- a return value can be directly assigned to variables.

----------
##### Last version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-method-return-value-in-ruby" target="_blank">https://www.alexindev.com/posts/an-introduction-to-method-return-value-in-ruby</a>
