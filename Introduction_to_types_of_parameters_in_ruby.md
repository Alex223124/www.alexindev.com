##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/introduction-to-types-of-parameters-in-ruby" target="_blank">https://www.alexindev.com/posts/introduction-to-types-of-parameters-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Introduction to types of parameters in ruby

When we want to write a method, we often ask ourselves the following questions: "Which parameters are best for this method? In what order is it better to define these parameters? Is it possible to pass arguments to a method using another approach?". In ruby, there is a large variety of parameters that we can define.

In this article, I decided to make a list of links to related articles in which we will in detail consider the cases of using various parameters. Also, I decided to specify a basic example for each kind of parameters so that it can be found faster if necessary.

----------
## 1.Methods without parameters
Link: https://www.alexindev.com/posts/ruby-methods-without-parameters

Example:

	def method_name
	  puts "method body"
	end

	method_name

produces:

	method body
	=> nil


----------
## 2.Positional parameters
Link: https://www.alexindev.com/posts/positional-parameters-in-ruby

Example:

	def method(positional_parameter_one, positional_parameter_two)
	  puts positional_parameter_one
	  puts positional_parameter_two
	end

	method(1, 2)

produces:

	1
	2
	=> nil


----------
## 3.Last positional parameter = hash parameter
Link: https://www.alexindev.com/posts/in-ruby-last-positional-parameter-hash-parameter

Example:

	def method_name(first_parameter, second_parameter)
	  puts first_parameter
	  puts second_parameter.inspect
	  puts second_parameter.class
	end

	method_name("first argument", first_hash: "Value for first hash", second_hash: "Value for second hash", third_hash: "Value for third hash")

produces:

	first argument
	{:first_hash=>"Value for first hash", :sec>"Value for second hash", :third_hash=>"Value for third hash"}
	Hash
	=> nil


----------
## 4.Parameters with default values

Link: https://www.alexindev.com/posts/parameters-with-default-values-in-ruby

Example:

	def method_name(parameter_one = "default value one", parameter_two = "default value two")
	  puts parameter_one
	  puts parameter_two
	end

	method_name

produces:

	default value one
	default value two
	=> nil


----------
## 5.Keyword parameters
Link: https://www.alexindev.com/posts/keyword-parameters-in-ruby

Example:

	def method_name(parameter_one:, parameter_two:, parameter_three:)
	  puts parameter_one
	  puts parameter_two
	  puts parameter_three
	end

	method_name(parameter_one: "first argument", parameter_two: "second argument", parameter_three: "third argument")

produces:

	first argument
	second argument
	third argument
	=> nil


----------
## 6.Parameter with splat operator (*)
Link (part 1/2): https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-1-2
Link (part 2/2): https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-2-2

Example:

	def method_name(*parameters)
	  puts parameters
	end

	method_name("first argument")
	puts "----"
	method_name("first argument", "second argument")
	puts "----"
	method_name("first argument", "second argument", "third argument")

produces:

	first argument
	----
	first argument
	second argument
	----
	first argument
	second argument
	third argument
	=> nil


----------
## 7.Anonymous splat parameter
Link: https://www.alexindev.com/posts/anonymous-splat-parameter-in-ruby

Example:

	def method_name(*)
	  puts "method body"
	end

	method_name("first argument", "second argument", ["third", "argument"])

produces:

	method body
	=> nil


----------
## 8.Parameter with double splat operator (**)
Link: https://www.alexindev.com/posts/parameter-with-double-splat-operator-in-ruby

Example:

	def method_name(**collection)
	  collection.each do |key, value|
	    puts key
	    puts value
	  end
	end

	collection = {
	  "Key one": "Value one",
	  "Key two": "Value two",
	  "Key three": "Value three"
	}

	method_name(collection)

produces:

	Key one
	Value one
	Key two
	Value two
	Key three
	Value three
	=> {:"Key one"=>"Value one", :"Key two"=>"Value two", :"Key three"=>"Value three"}


----------
## 9.Parameter with ampersand operator (&)
Link: https://www.alexindev.com/posts/parameter-with-ampersand-operator-in-ruby

Example:

	def method_name(first_parameter, &block)
	  puts block.class
	  puts first_parameter
	end

	method_name("First argument") { puts "Hello from block!" }

produces:

	Proc
	First argument
	=> nil

  ----------

##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/introduction-to-types-of-parameters-in-ruby" target="_blank">https://www.alexindev.com/posts/introduction-to-types-of-parameters-in-ruby</a>
