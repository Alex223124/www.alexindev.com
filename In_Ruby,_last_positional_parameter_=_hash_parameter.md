##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/in-ruby-last-positional-parameter-hash-parameter" target="_blank">https://www.alexindev.com/posts/in-ruby-last-positional-parameter-hash-parameter</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## In Ruby, last positional parameter = hash parameter

In this article, we'll talk about the features of a last positional parameter in ruby.

----------
This article is divided into the following sections:

 1. [A last positional parameter works as a hash parameter](#a-last-positional-parameter-works-as-a-hash-parameter)
 2. [Most likely it's syntactic sugar](#most-likely-its-syntactic-sugar)
 3. [This feature works only with the positional parameter and only with the last one](#this-feature-works-only-with-the-positional-parameter-and-only-with-the-last-one)
 4. [If there are several arguments, then other objects should necessarily go before the hash](#if-there-are-several-arguments-then-other-objects-should-necessarily-go-before-the-hash)
 5. [If we assign each hash to a separate variable, they will be treated as separate objects](#if-we-assign-each-hash-to-a-separate-variable-they-will-be-treated-as-separate-objects)
 6. [Conclusions](#conclusions)

----------
## A last positional parameter works as a hash parameter
To understand how this works, let's see an example. For example, we have a method with several parameters, and when we call this method, we pass more than two parameters from which the last few are hashes.

Our code:

	def test_method(first_parameter, second_parameter)
	  puts first_parameter
	  puts second_parameter.inspect
	  puts second_parameter.class
	end

	test_method("first argument", first_hash: "Value for first hash", second_hash: "Value for second hash", third_hash: "Value for third hash")

produces:

	first argument
	{:first_hash=>"Value for first hash", :second_hash =>"Value for second hash", :third_hash=>"Value for third hash"}
	Hash
	=> nil  

What can we note from the example? All 3 hashes were assigned to the local variable 'second_parameter' and combined into one hash. Thus, if we want to transfer a large number of hashes to a method, we can pass them as is. Ruby will automatically determine where and what to assign.


----------
## Most likely it's syntactic sugar
As in many cases when ruby simplifies writing code, this syntax can be considered as syntactic sugar. Without it, the code would look a little different:

	def test_method(first_parameter, second_parameter)
	  puts first_parameter
	  puts second_parameter.inspect
	  puts second_parameter.class
	end

	test_method("first argument", {first_hash: "Value for first hash", second_hash: "Value for second hash", third_hash: "Value for third hash"})

produces:

	first argument
	{:first_hash=>"Value for first hash", :second_hash =>"Value for second hash", :third_hash=>"Value for third hash"}
	Hash
	=> nil   

As we see, we got exactly the same result as in the very first example.


----------
## This feature works only with the positional parameter and only with the last one
For example, we can try to pass through the arguments hashes as first arguments and then other kinds of arguments. In this case, ruby will show us an error.

Our example:

	def test_method(first_parameter, second_parameter, third_parameter)
	  puts "Value of first_parameter: #{first_parameter}"
	  puts "Value of second_parameter: #{second_parameter}"
	  puts "Value of third_parameter: #{third_parameter}"
	end

	test_method(first_argument: "first argument", second_argument: "second argument", third_argument: "third argument", "fourth argument")
produces:

	(repl):7: syntax error, unexpected ')', expecting =>
	...d argument", "fourth argument")
	...                               ^
The same thing will happen if the hashes are in the center of the argument list:

	def test_method(first_parameter, second_parameter, third_parameter)
	  puts "Value of first_parameter: #{first_parameter}"
	  puts "Value of second_parameter: #{second_parameter}"
	  puts "Value of third_parameter: #{third_parameter}"
	end

	test_method("first argument", second_argument: "second argument", third_argument: "third argument", fourth_argument: "fourth argument", "last argument")

produces:

	(repl):7: syntax error, unexpected ')', expecting =>
	...rth argument", "last argument")
	...                               ^
We can also try other kinds of parameters, for example, the keyword parameter:

	def get_hash_as_parameter(first_parameter, some_parameter, keyword_parameter: )
	  puts "Value of first_parameter: #{first_parameter}"
	  puts "Value of some_parameter: #{some_parameter}"
	  puts "Value of keyword_parameter: #{keyword_parameter}"
	end

	get_hash_as_parameter("first argument",  hash_one: "Value for hash one", hash_two: "Value for hash two", keyword_parameter: "Value of keyword parameter"  )
produces:


	missing keyword: keyword_parameter
	(repl):1:in `get_hash_as_parameter'
	(repl):7:in `<main>'  
As you can see, ruby didn't found arguments for the keyword parameter because it decided that the last argument is part of one hash.

We will see the same effect if we use a parameter with the splat operator.

Let's see an example:

	def get_hash_as_parameter(first_parameter, some_parameter, third_parameter, *some_amount_of_arguments)
	  puts "Value of first_parameter: #{first_parameter}"
	  puts "Value of some_parameter: #{some_parameter}"
	  puts "Value of third_parameter: #{third_parameter}"
	  puts "Value of some_amount_of_arguments: #{some_amount_of_arguments}"
	end

	get_hash_as_parameter("first argument", "second argument", hash_one: "Value for hash one", hash_two: "Value for hash two", hash_three: "Value for hash three", "something", "1234" )

Description of the code:

 1. We defined the get_hash_as_parameter method with 4 parameters: first_parameter, some_parameter, third_parameter, * some_amount_of_arguments;
 2. The last parameter is a parameter with the splat (*) operator (used when we want to pass an unlimited number of arguments to a method);
 3. The last argument is the instance of the String class.

Let's run our example:

	(repl):8: syntax error, unexpected ',', expecting =>
	...e for hash three", "something", "1234" )
	...                 


----------
## If there are several arguments, then other objects should necessarily go before the hash
Let's define a method with several positional parameters and pass to it a lot of hashes as arguments.

Our code:

	def test_method(first_parameter, second_parameter, third_parameter)
	  puts "Value of first_parameter: #{first_parameter}"
	  puts "Value of second_parameter: #{second_parameter}"
	  puts "Value of third_parameter: #{third_parameter}"
	end

	test_method("first argument", second_argument: "second argument", third_argument: "third argument", fourth_argument: "fourth argument")

Common sense tells us that the first two arguments must be used by the first two positional parameters, and the rest are accepted by the last parameter.

Let's run our code:

	wrong number of arguments (given 2, expected 3)
	(repl):1:in `test_method'
	(repl):7:in `<main>'   
As we can see, ruby can't separate hashes from other arguments and thinks that we passed 2 arguments.

Thus, before passing the hashes, we must pass other data structures for other parameters. Let's add one more argument after the first argument from the previous example. Let it be the string "second string":

	get_hash_as_parameter("first argument", "second string", second_argument: "second argument", third_argument: "third argument", fourth_argument: "fourth argument")
produces:

	Value of first_parameter: first argument
	Value of second_parameter: second string
	Value of third_parameter: {:second_argument =>"second argument", :third_argument=>"third argument", :fourth_argument=>"fourth argument"}
	=> nil  
As we can see, only after we placed another object (that belongs to another class) after the hash, ruby could understand how it should use arguments.


----------
## If we assign each hash to a separate variable, they will be treated as separate objects
The behavior of the last parameter, as a parameter that takes a hash, depends on what we pass in the argument list. As we know from the previous examples, if we pass hashes that are created by the literal constructor ({}), then the parameter will take all the hashes. But if we assign each hash to a local variable, then they will be understood as separate objects.

For instance:

	def get_hash_as_parameter(first_parameter, some_parameter)
	  puts first_parameter
	  puts some_parameter.inspect
	  puts some_parameter.class
	end

	hash_one = {first_hash: "Value for first hash"}
	second_hash = {second_hash: "Value for second hash"}
	third_hash = {third_hash: "Value for third hash"}

	get_hash_as_parameter("first argument", hash_one, second_hash, third_hash)

Description of the example:

 1. For this example, we defined 3 local variables: hash_one, second_hash, third_hash;
 2. All three variables are passed as     arguments.

Let's run our code:

	wrong number of arguments (given 4, expected 2)
	(repl):1:in `get_hash_as_parameter'
	(repl):11:in `<main>'  

As we see, the error text indicates that the method expected 2 arguments, and we passed 4. Thus, this feature of the language works only when we initialize the hashes directly in the argument list.


----------
## Conclusions
- a last positional parameter works as a hash parameter;
- hash parameter allows us to take multiple hashes as a single object;
- this feature works only with the positional parameter and only with the last one;
- if there are several arguments, then other objects should necessarily go before the hash;
- if we assign each hash to a separate variable, they will be treated as separate objects.

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/in-ruby-last-positional-parameter-hash-parameter" target="_blank">https://www.alexindev.com/posts/in-ruby-last-positional-parameter-hash-parameter</a>
