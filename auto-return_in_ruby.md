##### Last version of this article you can find here:  <a href="https://www.alexindev.com/posts/auto-return-in-ruby" target="_blank">https://www.alexindev.com/posts/auto-return-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Auto-return in ruby

In this article, we will consider the characteristics of the work of the implicit return from a method.

----------
This article is divided into the following sections:

 1. [By default, a return value is the result of the last line of a method](#by-default-a-return-value-is-the-result-of-the-last-line-of-a-method)
 2. [An empty method returns nil](#an-empty-method-returns-nil)
 3. [The last line can be just an object](#the-last-line-can-be-just-an-object)
 4. [If we need to return multiple objects from a method, we can use an array](#if-we-need-to-return-multiple-objects-from-a-method-we-can-use-an-array)
 5. [In ruby, almost everything has a return value](#in-ruby-almost-everything-has-a-return-value)
 6. [Conclusions](#conclusions)

----------
## By default, a return value is the result of the last line of a method

Compared to other programming languages, this approach looks more comfortable. For instance, in JavaScript, if we don't define the return keyword, it will return 'undefined'.

Let's see an example:

	def sum(number_one,  number_two)
	  number_one
	  number_two
	  number_one + number_two
	end

	sum(1,2)

produces:

	=> 3  
We got the return value 3. Now let's swap the expressions and see what the method returns:

	def sum(number_one,  number_two)
	  number_one + number_two
	  number_two
	  number_one
	end

	sum(1,2)

produces:

	=> 1  
Thus, when we plan to return a certain value from a method, we should pay attention to the last line of a method.


----------
## An empty method returns nil

But in fact, there can be a situation when in a method there is no code? Let's suppose that some programmer decided to determine in advance several empty methods. Will the program be executed? In this case, the method will return nil.

Let's see an example:

	def blank_method
	end

	a = blank_method
	puts a.inspect
produces:

	nil
	=> nil


----------
## The last line can be just an object

Sometimes in articles, the phrases "last line of a method" and "the last expression of a method" are used interchangeably. Important only last line. And in this role can be even an instance of some class.

Let's see a few examples:

	def method_with_array_inside
	  ["I'm", "Array!"]
	end

	def method_with_hash_inside
	  { key_one: "Value 1",
	    key_two: "Value 2",
	    key_three: "Value 3"
	  }
	end

	def method_with_string_inside
	  "String String String"
	end

	a = method_with_array_inside
	puts "Value of 'a' #{a}"
	b = method_with_hash_inside
	puts "Value of 'b' #{b}"
	c = method_with_string_inside
	puts "Value of 'c' #{c}"

produces:

	Value of 'a' ["I'm", "Array!"]
	Value of 'b' {:key_one =>"Value 1", :key_two=>"Value 2", :key_three=>"Value 3"}
	Value of 'c' String String String
	=> nil   

As you can see, all objects were assigned to the corresponding local variables, after which we deduced them with the puts method.


----------
## If we need to return multiple objects from a method, we can use an array

In ruby there is a convenient way to return a lot of objects from a method, we can use an array in which we put all the objects that we want to return. For example, we have a method from which we want to return the result of 3 different expressions:

	def three_types_of_sum(a,b,c,d)
	  first_result = a + b
	  second_result = a + b + c
	  third_result = a + b + c + d
	  [first_result, second_result, third_result ]
	end

	a = three_types_of_sum(1,2,3,4)
	puts a.inspect

Description of the example:

 1. We defined a method that takes 4 parameters;
 2. In the body of the method, we have a few expressions, whose results are assigned to the local variables first_result, second_result, third_result;
 3. Then we put all 3 variables into an array so that all 3 objects are inside one;
 4. Then we call the method and assign its return value to the variable 'a';
 5. Then we see what we have inside the local variable using the puts method and the inspect method.

Let's run our code:

	[3, 6, 10]
	=> nil  
As expected, ruby returned the result of the last expression and inside the variable we have the array - [3, 6, 10]. It should be noted that in terms of design, in such situations it's better to split the method into 3 separate methods.

However, there are situations when we don't have the opportunity to split a large method (for example, we have to rewrite a large number of tests and other methods), in that case, using an array can be a very convenient solution.


----------
## In ruby, almost everything has a return value
It should be noted that almost everything in ruby has a return value. For instance, when we define a class, we get nil as the return value:

	class ClassName
	end
produces:

	=> nil   
When we define a module, we also get nil as the return value:

	module ModuleName
	end

produces:

	=> nil   
In older versions of ruby, a method definition returned nil, but from version 2.1.0, a method definition returns method name.

	def some_method
	end
produces:

	=> :some_method  
Why do we consider this feature of the language? This is useful for metaprogramming, for example when we want to change visibility of a method. For example, the private method takes a method name as its argument.

Example:

	class ExampleClass

	  def some_method_one
	    puts "I'm private!"
	  end

	  private :some_method_one

	end

The same thing we can do in another way:

	class ExampleClass

	  private def some_method_one
	    puts "I'm private!"
	  end

	end

So, in the last example, we call the method that takes the symbol as the argument, since the definition of the method will return the symbol.


----------
## Conclusions
- by default, a return value is the result of the last line of a method;
- an empty method returns nil;
- the last line can be just an object;
- if we need to return multiple objects from a method, we can use an array;
- in ruby, almost everything has a return value.

----------

##### Last version of this article you can find here:  <a href="https://www.alexindev.com/posts/auto-return-in-ruby" target="_blank">https://www.alexindev.com/posts/auto-return-in-ruby</a>
