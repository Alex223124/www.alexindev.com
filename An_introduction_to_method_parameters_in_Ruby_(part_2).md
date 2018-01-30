##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-method-parameters-in-ruby-part-2-2" target="_blank">https://www.alexindev.com/posts/an-introduction-to-method-parameters-in-ruby-part-2-2</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## An introduction to method parameters in Ruby (part 2/2)

This article is the second part of the introduction to the parameters in ruby. The purpose of this article, as well as the previous one, is to illustrate the general features of the parameters before going deeper into types of parameters.

----------
This article is divided into the following sections:

 1. [Parameters and arguments must match each other](#)
 2. [Parameters make the methods flexible and allow them to be reused with another set of data](#)
 3. [Make sure that you have information about the parameters of a method](#)
 4. [In some situations of misuse of parameters, an error message will not be displayed](#)
 5. [A method can define an unlimited number of parameters](#)
 6. [The order in which the arguments are passed to the method - from left to right](#)
 7. [Some parameters can use other parameters](#)
 8. [The parameter name should help to understand what needs to be passed to a method for correct work](#)
 9. [Conclusions](#)
----------
## Parameters and arguments must match each other
In ruby, there are a huge number of options for parameters that you can define for methods. It's important to understand that each parameter provides only certain possibilities for passing arguments to a method. The main reason for many errors is the difference between parameters that are determined by a method and the arguments that we are trying to pass to the method.

For instance, we have a method with a positional parameter and a keyword parameter:

	def print_parameters(parameter, second_parameter:)
	  puts parameter, second_parameter
	end

In this case, the possibility to pass 2 parameters is clearly defined.

Method call options that doesn't work:

	print_parameters("test string")

produces:

	missing keyword: second_parameter
	(repl):1:in `print_parameters'
	(repl):5:in `<main>'   

Example with 2 arguments:

	print_parameters("test string", "second test string")

produces:

	wrong number of arguments (given 2, expected 1)
	(repl):1:in `print_parameters'
	(repl):5:in `<main>'   

With one argument for keyword parameter:

	print_parameters(second_parameter: "second test string")

produces:

	missing keyword: second_parameter
	(repl):1:in `print_parameters'
	(repl):5:in `<main>'   

Right option:

	print_parameters("test string", second_parameter: "second test string")

produces:

	test string
	second test string
	=> nil   

Thus, it's necessary to know what opportunities are given by what kinds of parameters. We will talk about different types of parameters in next articles more closely.


----------
## Parameters make the methods flexible and allow them to be reused with another set of data
What is the purpose of the parameters? The purpose of the parameters is to pass a values inside a methods so that we can work with them in the method. This mechanism allows us to use the methods many times. If the methods couldn't have an ability to accept data and worked only with static values, then we would have to create much more methods.

For instance, we can write the code like this:


	class Example

	  def value
	    @value
	  end

	  def value=(value)
	    @value = value
	  end

	  def multiply_by_one
	    puts self.value * 1
	  end

	  def multiply_by_two
	    puts self.value * 2
	  end

	  def multiply_by_three
	    puts self.value * 3
	  end

	  def multiply_by_four
	    puts self.value * 4
	  end

	end
Description of the code:

- We defined class Example;
- We also defined two methods: value and value=,  with which we will manage the state of a new instance of the class;
- We also defined four methods: multiply_by_one, multiply_by_two, multiply_by_three, multiply_by_four;
- Each of these methods works the same way - it takes the value then multiplies that value by a certain number.

Let's use this class:

	new_instance = Example.new
	new_instance.value = 2
	new_instance.multiply_by_one
	new_instance.multiply_by_two
	new_instance.multiply_by_three
	new_instance.multiply_by_four

produces:


	2
	4
	6
	8
	=> nil   

We can rewrite the whole previous class with less amount of code:


	class Example

	  def value
	    @value
	  end

	  def value=(value)
	    @value = value
	  end

	  def multiply(x)
	    puts self.value * x
	  end

	end

	new_instance = Example.new
	new_instance.value = 2
	new_instance.multiply(1)
	new_instance.multiply(2)
	new_instance.multiply(3)
	new_instance.multiply(4)

produces:

	2
	4
	6
	8
	=> nil   

Thus, by changing the value of the argument, we can influence on the return value from the method, which makes the methods a very flexible tool for solving problems.


----------
## Make sure that you have information about the parameters of a method
When you call a method, you need to know with what arguments it works. If you don't have reliable information then you will most likely run from error to error and waste time.

For instance, suppose there is a class Articles in which we have a method which should output a list of articles. We don't know how many arguments the method takes and we decide that one argument is enough. "It seems to me that one argument is enough for this method!" - a naive programmer might think. And since we don't know exactly how the method is implemented, we certainly get the error:

	new_article = Article.new
	new_article.list_of_articles("'Popular ruby gems'")

produces:

	wrong number of arguments (given 1, expected 3)
	(repl):3:in `list_of_articles'
	(repl):12:in `<main>'  

In this example, the error is caused because instead of 3 arguments we passed one. And the interpreter doesn't know how to execute the second and third expression that is inside the method. I know about the number of expressions in the method because I came up with the method in advance :).

Let's look at how this method is implemented:

	class Article

	  def list_of_articles(article_one, article_two, article_three)
	    puts "Article one is #{article_one}"
	    puts "Article two is #{article_two}"
	    puts "Article three is #{article_two}"
	  end

	end

Now, armed with knowledge, we are ready to use it for its intended purpose. The method has 3 positional parameters, therefore, when we call the method, we must pass 3 arguments to it:

	new_article = Article.new
	new_article.list_of_articles("'Popular ruby gems'", "'What is Ruby on Rails?'", "'What is rack?'" )

produces:

	Article one is 'Popular ruby gems'
	Article two is 'What is Ruby on Rails?'
	Article three is 'What is rack?'
	=> nil

Great! Thus, the faster you have information about what parameters are defined in the method, the faster you can use it.


----------
## In some situations of misuse of parameters, an error message will not be displayed
There are situations when the method can have some parameters that are necessary and some parameters that are optional. This situation is more problematic than the situation when we can see an error. For instance, we know that the Article class has the list_of_articles method to which you can submit a list of articles.

Let's pass on one article that we call 'Popular ruby gems' and see what happens:

	new_article = Article.new
	new_article.list_of_articles("'Popular ruby gems'")
produces:

	Article one is 'Popular ruby gems'
	Article two is 'What is Ruby on Rails?'
	Article three is 'What is rack?'
	=> nil   

Hmm ... It's strange, right? We passed only one title of the article. Where did the 2 names come from?

Let's see how this method is implemented:

	class Article

	  def list_of_articles(article ="'What is ruby?'", article_two="'What is Ruby on Rails?'", article_three="'What is rack?'")
	    puts "Article one is #{article_one}"
	    puts "Article two is #{article_two}"
	    puts "Article three is #{article_three}"
	  end

	end
As we see, the list_of_article method defines 3 default parameters, that means, that even if we don't pass anything to this method, it will still return a result:

	new_article = Article.new
	new_article.list_of_articles

produces:

	Article one is 'What is ruby?'
	Article two is 'What is Ruby on Rails?'
	Article three is 'What is rack?'
	=> nil   

Therefore, it's always important to know what parameters are defined in a method and how they work so that you can pick right arguments for the method and get a result.

When working with third-party libraries, in most cases there is an official documentation in which you can find what arguments a method takes. However, in some cases, you will have to open a library and look for methods manually, after which you can see what parameters are defined in the method what should help to call a method correctly. It is better to spend a little time searching for information than on guessing how a method should work.


----------
## A method can define an unlimited number of parameters
In ruby, methods don't have limitations on the number of parameters. However, it's good practice to keep the list of parameters as small as possible.

Example of a method with 20 parameters:

	def print_each_object(a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t)
	  puts a
	  puts b
	  puts c
	  puts d
	  puts e
	  puts f
	  puts g
	  puts h
	  puts i
	  puts j
	  puts k
	  puts l
	  puts m
	  puts n
	  puts o
	  puts p
	  puts q
	  puts r
	  puts s
	  puts t
	end

	print_each_object('a','b','c','d','e','f','g','h','i','j','k','l','m','n','o','p','q','r','s','t')

produces:

	a
	b
	c
	d
	e
	f
	g
	h
	i
	j
	k
	l
	m
	n
	o
	p
	q
	r
	s
	t
	=> nil   

As you can see, this method isn't very readable, even if the names of the variables made sense, it would still be very cumbersome.

In cases where there are too many parameters, it is recommended to try to divide the method into several smaller methods, or use other parameters. We can rewrite the previous example using "splat" parameter (it's used in cases of an undefined number of arguments):

	def print_each_object(*objects)
	  objects.each do |obejct|
	    puts object
	  end
	end

	print_each_object('a','b','c','d','e','f','g','h','i','j','k','l','m','n','o','p','q','r','s','t')

produces:


	a
	b
	c
	d
	e
	f
	g
	h
	i
	j
	k
	l
	m
	n
	o
	p
	q
	r
	s
	t
	=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t"]   

As you can see, ruby is ready even for an unlimited number of arguments.


----------
## The order in which the arguments are passed to the method - from left to right
The order in which the parameters are interpreted in the method also deserves a separate consideration, as this will help to avoid unnecessary errors. The order in which the arguments are passed to the method is from left to right. This means that we can think in advance how the arguments will be represented inside the method.

For instance, we have the print_parameters method:

	def print_parameters(first_parameter, second_parameter, third_parameter)
	  puts first_parameter
	  puts second_parameter
	  puts third_parameter
	end

	print_parameters("I will be first!", "I will be second!", "I will be third!")

produces:

	I will be first!
	I will be second!
	I will be third!
	=> nil   
now, let's swap the arguments:

	print_parameters("I will be third!", "I will be second!", "I will be first!")

produces:

	I will be third!
	I will be second!
	I will be first!
	=> nil
As you can see, changing the places of the arguments influenced the return value.


----------
## Some parameters can use other parameters
Understanding the order in which the arguments are used can help us if we decide to define a parameter that will use some of the previous parameters.

For instance:

	def print_parameters(first_parameter, second_parameter, third_parameter = second_parameter.gsub('second', 'third') )
	  puts first_parameter
	  puts second_parameter
	  puts third_parameter
	end

In this example, we have the method with 3 parameters.

The last parameter has the default value, which means that if we don't pass an argument for it, we will still have the local variable that will reference the result of the expression.

Let's use the method:

	print_parameters("I will be first", "I will be second!")

produces:

	I will be first
	I will be second!
	I will be third!
	=> nil   
In this example, we passed only 2 arguments to the method: "I will be first", "I will be second!", But the method found what to use for the last parameter.

Now let's put last parameter with the expression at the beginning of the list of parameters:

	def print_parameters(third_parameter = second_parameter.gsub('second', 'third'), first_parameter, second_parameter )
	  puts first_parameter
	  puts second_parameter
	  puts third_parameter
	end

	print_parameters("I will be first", "I will be second!")
produces:



	undefined local variable or method `second_parameter' for main:Object
	Did you mean?  second_parameter
	(repl):17:in `print_parameters'
	(repl):23:in `<main>'   

As we see, at that moment when ruby started to execute the method call, the variable second_parameter wasn't defined, so we received the corresponding error.

It should be noted that you shouldn't use expressions directly in the parameter list because it looks unreadable. This code was written only for educational purposes, do not blame me if you will see somewhere this approach :).


----------
## The parameter name should help to understand what needs to be passed to a method for correct work
It's necessary that the parameter name should tell what arguments needed to be passed to a method for correct work. It makes no sense to add chaotic parameters and hope that someone will understand this code. In small methods, this problem doesn't significantly interfere, but if a method has become very huge - it's likely that it will take a long time to understand about what it should get for the desired result.

For instance, we'll write a method that will perform the function of creating an order for a taxi service:

	def taxi_service(a, b)
	  puts "Taxi order: #{a} - #{b}"
	end

Because of the simplicity of the method, one can intuitively guess that the parameter 'a' means the landing point and the parameter 'b' the destination. Let's make the example a little difficult:

	def taxi_service(a, b, c, d)
	  puts "Taxi order: #{a} - #{b}; #{c} - #{d}"
	end


Now we have a method in which you can get confused. What can the parameters 'c' and 'd' mean and how do they relate to the first two parameters? This information can be found by searching for the use of the method, or by asking a developer who wrote this code. But if these options didn't work, then you should admit that you are confused.

Let's rewrite our example:

	def taxi_service(from, to, time_start, time_end)
	  puts "Taxi order: #{from} - #{to}; #{time_start} - #{time_end}"
	end
Now, the method is perfectly readable! The last two parameters indicate the time at which the taxi should arrive at the landing point.Let's use the method:

	taxi_service("Street 123", "Street 124","2am","2:15am")
produces:

	Taxi order: Street 123 - Street 124; 2am - 2:15am
	=> nil   
Thus, the more understandable a parameter name the faster another developer can understand your code and solve a problem.


----------
## Conclusions
- it's necessary to know what opportunities are given by what kinds of parameters;
- by changing the value of the argument, we can influence on the return value from the method, which makes the methods a very flexible tool for solving problems;
- the faster you have information about what parameters are defined in the method, the faster you can use it;
- It is better to spend a little time searching for information than on guessing how a method should work;
- ruby is ready even for an unlimited number of arguments;
- the order in which the arguments are passed to the method - from left to right;
- some parameters can use other parameters, but it's better to stay away from that;
- the more understandable a parameter name the faster another developer can understand your code and solve a problem.

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-method-parameters-in-ruby-part-2-2" target="_blank">https://www.alexindev.com/posts/an-introduction-to-method-parameters-in-ruby-part-2-2</a>
