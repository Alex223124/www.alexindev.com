##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameter-with-ampersand-operator-in-ruby" target="_blank">https://www.alexindev.com/posts/parameter-with-ampersand-operator-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Parameter with ampersand operator (&) in Ruby

In this article, we will consider the features of working with a parameter that is defined using the ampersand operator.

----------
This article is divided into the following sections:

 1. [Parameter with ampersand operator](#)
 2. [A block that we pass to a method is converted to Proc object](#)
 3. [A block can be called not only using the call method but also via keyword 'yield'](#)
 4. [Parameter with ampersand operator is optional](#)
 5. [If a method doesn't have a parameter with the ampersand operator (&), then the method can still accept a block](#)
 6. [Why use a parameter with the ampersand operator if we can already pass a block to a method?](#)
 7. [Parameter with the ampersand operator can be declared only once](#)
 8. [The parameter with the ampersand operator must be the last parameter in a parameter list](#)
 9. [A parameter with the ampersand operator is only suitable for blocks; Proc and Lambda can't be transmitted through it](#)
 10. [Conclusions](#)
----------
## Parameter with ampersand operator
Methods in ruby can take a block as an argument and use it inside a method. In order to define a block as a parameter ruby has syntax with ampersand operator (&).

For example, we have an expression:

	puts "Hello from block!"
which gives the following result:

	Hello from block!
	=> nil

Let's create a method and define the parameter with the ampersand operator (&):

	def print_phrase(&block)
	  block.call
	end

Now we pass there the block with the expression:

	print_phrase { puts "Hello from block!" }
as a result, we get the same return value as if we were using the expression itself:

	Hello from block!
	=> nil

Thus, using the syntax with the ampersand operator (&), we can define a parameter that takes a block.


----------
## A block that we pass to a method is converted to Proc object
Since blocks in ruby are not objects, you can have a logical question: "If local variables refer to objects, what will the local variable refer to if we pass a block?" We can easily find out to which class the value belongs to which is referenced by the local variable inside the method using the `class` method:

	def print_phrase(phrase, &block)
	  puts block.class
	  puts phrase
	end

	print_phrase("This is phrase") { puts "Hello from block!" }

produces:

	Proc
	This is phrase
	=> nil   

As you can see, the parameter with the ampersand operator (&) converts the transferred block into the object that belongs to Proc class.


----------
## A block can be called not only using the call method but also via keyword 'yield'
As we saw in the previous example, inside a method, you can use the call method to execute a code that is contained within a block.

	def print_phrase(&block)
	  block.call
	end

	print_phrase { puts "test test" }

produces:

	test test
	=> nil
The second way to call a block is to use the yield keyword. Even if we explicitly pass a block, we can still call it inside the method using the yield keyword.

Let's see an example. We will create a method called 'print_phrase' that will print a text a certain number of times. We will specify parameter which called 'amount' (a number of times that we want the method to output a phrase) and a block - '&block'. Also in the example, we will use the times method which will call the block a certain number of times.

Our example:

	def print_phrase(amount, &block)
	  amount.times.each do |x|
	    yield
	  end
	end

	print_phrase(5) { puts "Hello! I'm inside of the method!" }

Let's run it:

	Hello! I'm inside of the method!
	Hello! I'm inside of the method!
	Hello! I'm inside of the method!
	Hello! I'm inside of the method!
	Hello! I'm inside of the method!
	=> 5   
Thus, we have 2 options how we can call a block: the call method and the yield keyword.


----------
## Parameter with ampersand operator is optional
When using a parameter with the ampersand operator, the transfer of the block is not required. In other words, if a block is not passed after the list of arguments, then ruby will not see an error in this. However, it should be taken into account that inside a method the local variable that should reference to Proc object will refer to nil, what may cause incorrect work of a program.

	def print_phrases(a, &b)
	  puts a.inspect
	  puts b.inspect
	end

	print_phrases("first phrase")

produces:

	"first phrase"
	nil
	=> nil  



----------
## If a method doesn't have a parameter with the ampersand operator (&), then the method can still accept a block
Let's define two methods: 'print_phrase' and 'print_characters'. One method will have an ability to receive a block using the ampersand ('&block'), and the second one will not have any parameters at all. When we will call those methods, we will pass a block to each method and try to call it using the yield keyword.

Our methods:

	def print_phrase
	  puts yield
	end

	def print_characters(&block)
	  puts yield
	end

	print_phrase { "Example phrase" }
	print_characters { "a b c d e f g" }

produces:

	Example phrase
	a b c d e f g
	=> nil
As we can see, in both cases the code was successfully launched.

----------

## Why use a parameter with the ampersand operator if we can already pass a block to a method?
I can identify 2 obvious reasons for using a parameter with the ampersand operator.

**Cause 1. We need an object for our logic.**

This can be important if we want to have an object inside a method to solve a problem. Anonymous block doesn't have so many methods and possibilities that can be done with it in comparison with an object.
For instance, by a condition of a problem, we need to use a code from a block in certain cases, and in others, we should return Proc as a return value:

	def print_characters(a, &proc)
	  if a > 2
	    proc
	  else
	    proc.call
	  end
	end

	a = print_characters(1) { "a b c d e f g" }
	puts a
	puts a.class
	puts "======================================"
	a = print_characters(3) { "a b c d e f g" }
	puts a
	puts a.class
	puts a.call

produces:

	a b c d e f g
	String
	======================================
	#<Proc:0x0056277d92ebe0@(repl):13>
	Proc
	a b c d e f g
	=> nil   

**Cause 2. When a block is defined in parameters, the method is more readable**

When we explicitly determine an ability of a method to accept a block, then the other programmer is likely to be more quickly oriented in your code. Since it's extremely rare in the ordinary practice when someone simply defines parameters and doesn't use them in a method.


----------
## Parameter with the ampersand operator can be declared only once
Since the methods in ruby can take only one block, this restriction also applies to a parameter with the ampersand operator. We can only define it once and transfer one block.
For example, if we will define a method with 2 such parameters, we will see an error:

	def method_with_two_blocks(&block_one, &block_two)
	  block_one.call
	  block_two.call
	end

produces:

	(repl):1: syntax error, unexpected ',', expecting ')'
	def method_with_two_blocks(&block_one, &block_two)
	                                      ^



----------
## The parameter with the ampersand operator must be the last parameter in a parameter list
In ruby, when we pass a block to a method, it should always go after an argument list:

	some_method(argument_one, argument_two, argument_three) { puts "block block block" }

Similarly, when we use a parameter with the ampersand operator, we must put it as the last parameter in a parameter list. To begin with, let's see an example of a method when all conditions are met.
From this example, we will move further so that we have something to compare wrong options with:

	def print_phrase(phrase_one, phrase_two, &block)
	  block.call
	  puts phrase_one
	  puts phrase_two
	end

	print_phrase("This is phrase one", "This is phrase two") { puts "Hello from block!" }

produces:

	Hello from block!
	This is phrase one
	This is phrase two
	=> nil

Now, let's look at the example when a parameter with the ampersand operator comes first in a list of parameters:

	def print_phrase(&block, phrase_one, phrase_two)
	  block.call
	  puts a
	end

	print_phrase("This is phrase one", "This is phrase two") { puts "Hello from block!" }

Let's run it and see what happens:

	(repl):22: syntax error, unexpected ',', expecting ')'
	def print_phrase(&block, phrase_one, phrase_two)
	                        ^
	(repl):22: syntax error, unexpected ')', expecting '='
	(repl):25: syntax error, unexpected keyword_end, expecting end-of-input
The same will happen if a parameter with the ampersand operator will be in the center of a list of parameters. Thus, the only correct place for this parameter is the end of a parameter list.


----------
## A parameter with the ampersand operator is only suitable for blocks; Proc and Lambda can't be transmitted through it
A parameter with the ampersand operator works only with blocks. If we want to transfer something else, for example, instances of Proc or Lambda , then we'll see an error.

Let's try all 3 options. For example, we have 'print_phrases' method:

	def print_phrases(a, &b)
	  puts a
	  puts b.call
	end

Let's try to pass a lambda:

	print_phrases("first phrase", lambda {"11111111"})

produces:

	wrong number of arguments (given 2, expected 1)
	(repl):1:in `print_phrases'
	(repl):6:in `<main>'

Let's try to pass a proc object:

	print_phrases("first phrase", Proc.new {"11111111"})

produces:

	wrong number of arguments (given 2, expected 1)
	(repl):1:in `print_phrases'
	(repl):6:in `<main>'  

Let's try to pass a block:

	print_phrases("first phrase") {"second phrase"}
produces:

	first phrase
	second phrase
	=> nil  


----------
## Conclusions
- In ruby, we can define a special parameter using the ampersand (&) operator that handles the blocks
- A block that we pass to a method is converted to Proc object
- Inside a method, we can call the block using the yield keyword and the call method
- Parameter with ampersand operator is optional
- Methods in ruby by default accept blocks (parameters are not required for this)
- Parameter with the ampersand operator can be declared only once
- The parameter with the ampersand operator must be the last parameter in a parameter list
- A parameter with the ampersand operator is only suitable for blocks


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameter-with-ampersand-operator-in-ruby" target="_blank">https://www.alexindev.com/posts/parameter-with-ampersand-operator-in-ruby</a>
