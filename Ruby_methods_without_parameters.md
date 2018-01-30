##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/ruby-methods-without-parameters" target="_blank">https://www.alexindev.com/posts/ruby-methods-without-parameters</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Ruby methods without parameters

In this article, we will take a look at methods that don't have any parameters.

----------
This article is divided into the following sections:

 1. [Syntax](#)
 2. [If you pass some arguments, then ruby will display an error](#)
 3. [Even if a method doesn't have defined parameters, the method still takes a block](#)
 4. [Conclusions](#)

----------
## Syntax
Let's begin the classification of methods by parameters from the simplest case - when a method has no parameters at all. You already could saw similar methods before, let's look at them again:

	def cat
	  puts "Meow! Meow!"
	end

	def dog
	  puts "Raf! Raf!"
	end

	def cow
	  puts "Moooooo!"
	end

	cat
	dog
	cow
produces:

	Meow! Meow!
	Raf! Raf!
	Moooooo!
	=> nil   


----------
## If you pass some arguments, then ruby will display an error
In some programming languages, if we pass arguments, and they are not defined in the list of parameters, then this is not considered as an error - the code will still be executed. In ruby we have the different situation, if you want to pass arguments to a method for which there are no parameters, then the program will terminate its execution.

Example:

	def cat
	  puts "Meow! Meow!"
	end

	cat("argument")
produces:

	wrong number of arguments (given 1, expected 0)
	(repl):1:in `cat'
	(repl):5:in `<main>'   


----------
## Even if a method doesn't have defined parameters, the method still takes a block
Let's see how it looks like:

	def cat
	  puts "Meow! Meow!"
	  yield
	end

	cat { puts "Hello from block!" }

produces:

	Meow! Meow!
	Hello from block!
	=> nil   



----------
## Conclusions
- in ruby, as in other programming languages, there are methods that haven't parameters;
- a method without parameters can receive a block;
- if we pass arguments to a method which haven't parameters, then ruby will stop the code execution and show us an error.

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/ruby-methods-without-parameters" target="_blank">https://www.alexindev.com/posts/ruby-methods-without-parameters</a>
