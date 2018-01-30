##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/positional-parameters-in-ruby" target="_blank">https://www.alexindev.com/posts/positional-parameters-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Positional parameters in Ruby

In this article, we will consider the features of working with the positional parameters.

----------
This article is divided into the following sections:

 1. [What is a positional parameter?](#)
 2. [The presence of arguments for the positional parameter is required](#)
 3. [Conclusions](#)
----------
## What is a positional parameter?

Positional parameters are the simplest parameters in ruby, as their behavior is very predictable. The positional parameter makes it possible to transfer any object to a method and use it within the method.

Let's see declaring syntax:

	def method(positional_parameter_one, positional_parameter_two)
	  positional_parameter_one + positional_parameter_two
	end

	method(1, 2)
produces:

	=> 3   


----------
## The presence of arguments for the positional parameter is required
When calling a method that has positional parameters, the presence of arguments for these parameters is required. If we don't pass any arguments, then ruby will not know where to get values for local variables inside a method and will tell us about the error.

For instance:

	def method(positional_parameter_one, positional_parameter_two)
	  positional_parameter_one + positional_parameter_two
	end
As you can see, we have 2 positional parameters. Now, if we call a method with only one argument, we'll see the error:

	method(1)
produces:

	wrong number of arguments (given 1, expected 2)
	(repl):2:in `method'
	(repl):6:in `<main>'


----------
## Conclusions
- positional parameters are the most predictable parameters in ruby
- the presence of arguments for the positional parameter is required

----------

##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/positional-parameters-in-ruby" target="_blank">https://www.alexindev.com/posts/positional-parameters-in-ruby</a>
