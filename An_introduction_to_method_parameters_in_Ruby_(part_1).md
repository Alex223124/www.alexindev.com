##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-method-parameters-in-ruby-part-1-2" target="_blank">https://www.alexindev.com/posts/an-introduction-to-method-parameters-in-ruby-part-1-2</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## An introduction to method parameters in Ruby (part 1/2)

This article is the first article with various general points about parameters in ruby. The goals of this article are to illustrate what are the parameters in ruby and to answer common questions about how they are used.

----------
This article is divided into the following sections:

 1. [Methods in ruby have parameters](#)
 2. [What is a parameter?](#)
 3. [Parameters should be defined before use of a method](#)
 4. [Parameters are separated by a comma](#)
 5. [Parentheses for parameter definition are optional](#)
 6. [Parameters must be added to a method when the necessary data is inaccessible within the method](#)
 7. [The entire group of parameters that the method has is called a list of parameters](#)
 8. [Parameters and arguments are different entities](#)
 9. [When we pass arguments to a method, the method creates a local variable which has the same name](#)
 10. [In ruby, arguments inside a method are passed by reference](#)
 11. [Conclusions](#)
----------
## Methods in ruby have parameters
You could already saw a lot of methods that don't take any parameters.

For instance:

	"example".capitalize!
	"example two".swapcase!
	"example three".reverse
In this example, the methods: capitalize !, swapcase! and reverse hasn't parameters and are called on the "example", "example two" and "example three" objects.

At the same time, there are a lot of methods that expect data for correct work. For instance, arithmetic methods:

	2 + 2
	5 * 5
	10 / 2
or, if we rewrite them without using syntactic sugar:

	2+(2)
	5*(5)
	10/(2)

If we do not pass any arguments to these methods (+, *, /), then we will see errors:

	2+
produces:

	(repl):1: syntax error, unexpected end-of-input


There are also methods that will work without arguments, for example, the puts method:

	puts
produces:

=> nil   

Therefore, in ruby, as in many other programming languages, methods can have parameters. Let's define a print_phrase method with parameter 'phrase':

	def print_phrase(phrase)
	  puts phrase
	end

Now we can call the method and pass a data to it:

	print_phrase("some phrase")
produces:


	some phrase
	=> nil


----------
## What is a parameter?
Parameters in ruby are variables that are defined in method definition and which represent the ability of a method to accept arguments. So, if we will not have the appropriate parameters, then we will not be able to pass arguments to a method that will contain the data we need.

For instance, we have the same print_phrase method from the previous example:

	def print_phrase(phrase)
	  puts phrase
	end

since the method has the parameter, we can pass the value to it:

	print_phrase("some text")
produces:

	some text
	=> nil   
But what if we remove the phrase parameter? Will the method work as before?

	def print_phrase
	  puts phrase
	end

	print_phrase("some text")

produces:

	wrong number of arguments (given 1, expected 0)
	(repl):1:in `print_phrase'
	(repl):5:in `<main>'   

As you can see, without the parameter the method can't take the argument and ruby shows as the error.


----------
## Parameters should be defined before use of a method
Parameters must be defined exactly with the definition of the method. If we want to define the same method with other parameters, it will overwrite the previous one. But the effect will not be summed up in any way.

For instance, this code will not work:

	def print_parameter
	  puts parameter
	end

	print_parameter("test text")

	def print_parameter(parameter)
	  puts parameter
	end

produces:

	wrong number of arguments (given 1, expected 0)
	(repl):1:in `print_parameter'
	(repl):5:in `<main>'   

Therefore, it is necessary to decide in advance what parameters a method should contain.


----------
## Parameters are separated by a comma
When we want to define more than one parameter, we must use a comma(,).

It looks like this:

	def method_name(parameter_one, parameter_two, parameter_three)
	  puts parameter_one, parameter_two, parameter_three
	end


----------
## Parentheses for parameter definition are optional
When we define the parameters, we can do it without parentheses.

For instance:


	def method_name a,b,c,d
	  puts a,b,c,d
	end

	method_name 1,2,3,4

produces:

	1
	2
	3
	4
	=> nil   
However, it's usually recommended to use them, since a method with parenthesis looks more readable:

	def method_name(a,b,c,d)
	  puts a,b,c,d
	end


----------

## Parameters must be added to a method when a necessary data is inaccessible within the method
Since there are methods in which we do not use parameters, the logical question is: "How to determine whether we need parameters? Can the method work without them?"

For example, we have CarsManufacture class in which we have DEFAULT_COLOR constant, which denotes the standard factory color of the cars that this particular manufacturer makes:


	class CarsManufacture

	  DEFAULT_COLOR = "black"

	  def paint_car_in_default_color
	    puts "The car is painted in #{DEFAULT_COLOR}."
	  end

	end

	new_car = CarsManufacture.new
	new_car.paint_car_in_default_color

produces:

	The car is painted in black.
	=> nil

In this example, the paint_car_in_default_color method doesn't need parameters since we already have the constant DEFAULT_COLOR and we can use it inside the method. But what if we want to be able to paint cars in other colors?  Here we can't compensate all possible sets of colors by using the constants, i.e. we need the parameters.

Let's create a new method and add the color parameter:

	class CarsManufacture

	  DEFAULT_COLOR = "black"

	  def paint_car_in_default_color
	    paint_car_in(DEFAULT_COLOR)
	  end

	  def paint_car_in(color)
	    puts "The car is painted in #{color}."
	  end

	end

	new_car = CarsManufacture.new
	new_car.paint_car_in_custom_color('red')

produces:

	The car is painted in red.
	=> nil

Thus, the list of parameters is optional, if the method perfectly performs its function without parameters, then there is no need to add extra parameters. At the same time, if there is no way out, then it's worth using the parameters.


----------
## The entire group of parameters that the method has is called a list of parameters
Again, it's more about how to call things. The entire group of parameters that are defined in a method is called a list of parameters.

For instance, we have a method:

	def some_test_method(parameter_one, parameter_two, parameter_three)
	end

In this case, the list of parameters will consist of everything that is in parenthesis: parameter_one, parameter_two, parameter_three.


----------
## Parameters and arguments are different entities
Quite often in the articles, parameters and arguments are used interchangeably. It's necessary to distinguish such entities.

**The entity that we can pass to the method during the call is called an argument.
The entity that we declare when we define a method is called a parameter.**

For instance we have the method print_first_object:

	def print_first_object(collection)
	  puts collection[0]
	end

In this case 'collection' - name of parameter.

Let's use this method:

	argument = ["first", "second", "third"]
	print_first_object(argument)

produces:

	first
	=> nil   

In this case, the local variable 'argument' is the argument that we pass to the method when it is called. As arguments we can also use objects.

For instance, we can pass the array directly without using a variable:

	print_first_object(["first", "second", "third"])

produces:

	first
	=> nil   

Thus, we define parameters for a method, and arguments - we pass to a method.


----------
## When we pass arguments to a method, the method creates a local variable which has the same name
As you might have noticed, inside the method we use a variable that has the same name as a parameter.  This is because when we pass an argument to a particular method, ruby automatically creates a local variable inside the method which will refer to the object that we passed(it's true for most types of parameters, but there are also some exceptions about which we will talk in next articles).

For instance:

	def print_phrases(phrase_one, phrase_two, phrase_three)
	  puts "local variable phrase_one value is: #{phrase_one}"
	  puts "local variable phrase_two value is: #{phrase_two}"
	  puts "local variable phrase_three value is: #{phrase_three}"
	end

	print_phrases("first first first", "second second second", "third third third")

produces:

	local variable phrase_one value is: first first first
	local variable phrase_two value is: second second second
	local variable phrase_three value is: third third third
	=> nil

As we can see, all variables inside the method are accessible because they were defined as parameters.


----------
## In ruby, arguments inside a method are passed by reference
It's a very important point because in some programming languages the arguments inside the method are passed by value, what influence on a code in many ways. In ruby, we have a different situation, the variable that we have inside the method stores a reference to an object.
Thus, if we will change an object inside the method, then it will be changed also outside the method.

Let's try to see this feature of ruby in more detail:

	def change_case(x)
	  puts "New variable 'x' inside 'change_case' method created! The local variable 'x' refers to: #{x}"
	  x.upcase
	end

	def change_case!(x)
	  puts "New variable 'x' inside 'change_case!' method created! The local variable 'x' refers to: #{x}"
	  x.upcase!
	end

	a = "tex text text "
	puts "Value of local variable a: #{a}"
	puts change_case(a)
	puts "Value of local variable a after 'change_case' method: #{a}"
	puts change_case!(a)
	puts "Value of local variable a after 'change_case!' method: #{a}"


produces:

	Value of local variable a: tex text text
	New variable 'x' inside 'change_case' method created! The local variable 'x' refers to: tex text text
	TEX TEXT TEXT
	Value of local variable a after 'change_case' method: tex text text
	New variable 'x' inside 'change_case!' method created! The local variable 'x' refers to: tex text text
	TEX TEXT TEXT
	Value of local variable a after 'change_case!' method: TEX TEXT TEXT
	=> nil   

Description of the example:

- We defined 2 methods change_case and change_case!;
- The first method calls the upcase method on the object, but doesn't change its state, so the method returns a modified copy of the object; - And when we check a value of the 'a' variable, we get the unchanged string in the lower case - "text text text";
- Next, we call the change_case! method in which we pass the same string and as the return value we get the string "TEXT TEXT TEXT";
- Next, we check again on what value the variable 'a' refers to and see that now our line is written in uppercase - "TEXT TEXT TEXT".

What happened to the object? As was mentioned earlier, the method created a local variable that refers to the passed object, after which the object was changed inside the method. And since we changed it inside the method, then this object will have the same value outside the method.


----------
Conclusions
- in ruby, as in many other programming languages, methods have parameters;
- Parameters in ruby are variables that are defined in method definition and which represent the ability of a method to accept arguments;
- parameters are defined together with a method before use;
- if we have many parameters, then they should be separated by a comma (,);
- the parentheses for parameters in method definition are optional, but together with the parentheses the method is more readable;
- if the method perfectly performs its function without parameters, then there is no need to add extra parameters;
- the entire group of parameters that a method has is called a list of parameters;
- parameters and arguments are different entities, we define parameters for a method, and arguments - we pass to a method;
- when we pass an argument to a method, a local variable is created inside the method that has a name of a corresponding parameter;
- in ruby, arguments are passed by reference and by changing the object inside a method it will be also changed outside the method;


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-method-parameters-in-ruby-part-1-2" target="_blank">https://www.alexindev.com/posts/an-introduction-to-method-parameters-in-ruby-part-1-2</a>
