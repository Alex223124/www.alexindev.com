##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-1-2" target="_blank">https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-1-2</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Parameter with splat operator (*) in Ruby (part 1/2)

In this article, we'll look at the basic aspects of working with a parameter with the splat operator. Then, in the second part of this article, we will talk more closely about practical situations of using this type of parameter.

----------
This article is divided into the following sections:

 1. [A parameter with the splat operator allows us to work with an undefined number of arguments](#)
 2. [A parameter with the splat operator takes only those arguments for which there were no other parameters](#)
 3. [A parameter with the splat operator is optional](#)
 4. [A local variable will reference an empty array if arguments are not passed](#)
 5. [A parameter with the splat operator converts the arguments to an array within a method](#)
 6. [The arguments are passed in the same order in which they are specified when a method is called](#)
 7. [A method can't have two parameters with splat operator](#)
 8. [Conclusions](#)

----------
## A parameter with the splat operator allows us to work with an undefined number of arguments

Suppose a situation when we have 3 cooks. One of the cooks is an expert in his job and can make 3 large pizzas in half an hour. The second cook can make only 2 large pizzas at the same time. The third cook is a beginner cook. He can make only one large pizza.

Let's write a method that will choose which of the cooks will cook the pizza. But which parameters we should choose for the method? We don't know how many pizzas will be in the order and every pizza has its own name. In this case, a parameter with the splat operator will help us:

	def order_pizza(*pizzas)
	  if pizzas.count == 1
	    puts "1 pizza!"
	  elsif pizzas.count == 2
	    puts "2 pizzas!"
	  elsif pizzas.count == 3
	    puts "3 pizzas!"
	  end
	end

	order_pizza("Big pizza A")

	order_pizza("Big pizza A", "Big pizza B")

	order_pizza("Big pizza A", "Big pizza B", "Big pizza C")


produces:

	1 pizza!
	2 pizzas!
	3 pizzas!
	=> nil   
As you can see, the parameter with the splat operator performed the job. Note, we don't transfer the number of pizzas, we pass instances of the String class. First, we passed one instance, then second, then third. Thus, we use a parameter with the splat operator when we want to pass an undefined number of arguments to a method.

----------
## A parameter with the splat operator takes only those arguments for which there were no other parameters
For example, imagine that we have a method with one positional parameter and one parameter with the splat operator:

	def print_phrases(main_phrase, *phrases)
	  puts "This is main phrase: #{main_phrase}"
	  puts phrases
	end

	print_phrases("phrase one","phrase two", "phrase three", "phrase four", "phrase five")

produces:

	This is main phrase: phrase one
	phrase two
	phrase three
	phrase four
	phrase five
	=> nil  

As we can see, when we use a parameter with the splat operator, it collects all the arguments that go beyond the list of other parameters. Let's add another positional parameter and run our code again (the arguments are the same):

	def print_phrases(main_phrase, second_main_phrase, *phrases)
	  puts "This is main phrase: #{main_phrase}"
	  puts "Tihs is second main phrase: #{second_main_phrase}"
	  puts phrases
	end

	print_phrases("phrase one","phrase two", "phrase three", "phrase four", "phrase five")

produces:

	This is main phrase: phrase one
	Tihs is second main phrase: phrase two
	phrase three
	phrase four
	phrase five
	=> nil   
As we see, the parameter '*phrases' doesn't take other arguments, but takes only those that didn't find another place.


----------
## A parameter with the splat operator is optional
The parameter with the splat operator is optional, which means that if we don't pass the appropriate arguments to a method, ruby will not see an error in this.

	def print_phrases(main_phrase, *phrases)
	  puts "This is main phrase: #{main_phrase}"
	  puts phrases
	end

	print_phrases("phrase one")
produces:

	This is main phrase: phrase one
	=> nil  
However, if we don't pass arguments to other parameters, the error will still be.


----------
## A local variable will reference an empty array if arguments are not passed
If the corresponding arguments are not passed, then a local variable within a method will refer to an empty array.

For instance:

	def print_phrases(phrase_one, *phrases)
	  puts "This is first phrase: #{phrase_one}"
	  puts phrases.inspect
	end

	print_phrases("phrase one")

produces:

	This is first phrase: phrase one
	[]
	=> nil   


----------
## A parameter with the splat operator converts the arguments to an array within a method
Let's check to what class a value belongs to which a local variable will refer to when we use a parameter with splat operator.

For this purpose we will use the class method:

	def sum_of_all_args(one, two, *x)
	  puts x.class
	  sum = one + two
	  x.each { |nubmer| sum+=nubmer }
	  sum
	end

	sum_of_all_args(1,2,3,4,5,6,7,8,9,10)

produces:

	Array
	=> 55   
As you can see, the local variable 'x' refers to the value that belongs to the Array class.


----------
## The arguments are passed in the same order in which they are specified when a method is called
Arguments are passed to an array in the same order in which we specify them in an argument list when a method is called.

For instance:

	def print_words(*words)
	  words.each { |word| puts word }
	end

	print_words("one", "two", "three", "four")

produces:

	one
	two
	three
	four
	=> ["one", "two", "three", "four"]   
As we can see, the parameter didn't change the order in which we passed the arguments.


----------
## A method can't have two parameters with splat operator
Let's try to create a method with two parameters with splat operator.

	def print_books_and_articles(*books, *articles)
	  puts books
	  puts articles
	end

	print_books_and_articles("Ruby on Rails 4", "Ruby essentials", "Javascript essentials", "What is Ruby on Rails?", "What is Ruby?", "What is Javascript?")

produces:


	(repl):3: syntax error, unexpected *
	def print_books_and_articles(*books, *articles)
	                                      ^
	(repl):6: syntax error, unexpected keyword_end, expecting end-of-input

As you can see, ruby will not let us define such method.


----------
## Conclusions
- to handle an undefined number of arguments in ruby we have a parameter with the splat operator (*)
- a parameter with the splat operator takes only those arguments for which there were no other parameters
- a parameter with the splat operator is optional
- a parameter with the splat operator the arguments to an array
- if we do not pass any arguments for a parameter with the splat operator then a local variable within a method will reference to an empty array
- the arguments are passed in the same order in which they are specified when a method is called.
- a method can't have two parameters with splat operator at the same time

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-1-2" target="_blank">https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-1-2</a>
