##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-2-2" target="_blank">https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-2-2</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Parameter with splat operator (*) in Ruby (part 2/2)

In this article, we'll look at situations where a parameter with the splat operator can be useful to us, and we'll also look at its use with some other kinds of parameters.

----------
This article is divided into the following sections:

 1. [A parameter with splat operator is used when we want to transfer an undefined number of arguments](#a-parameter-with-splat-operator-is-used-when-we-want-to-transfer-an-undefined-number-of-arguments)
 2. [A parameter with the splat operator can reduce the amount of code](#a-parameter-with-the-splat-operator-can-reduce-the-amount-of-code)
 3. [It is used in constructors when working with collections](#it-is-used-in-constructors-when-working-with-collections)
 4. [In a parameter list, a parameter with the splat operator can be both before and after other parameters](#in-a-parameter-list-a-parameter-with-the-splat-operator-can-be-both-before-and-after-other-parameters)
 5. [Using a parameter with splat operator with a keyword parameter (a keyword parameter should be the last one)](#using-a-parameter-with-splat-operator-with-a-keyword-parameter-a-keyword-parameter-should-be-the-last-one)
 6. [Use with parameters with a default value](#use-with-parameters-with-a-default-value)
 7. [Conclusions](#conclusions)

----------
## A parameter with splat operator is used when we want to transfer an undefined number of arguments
Let's look at several different examples when we need to handle an undefined number of arguments.

**Example 1**
Imagine that you have encountered a situation when you don't know how many arguments will be transferred into a method. For example, your task is to accept various orders and send products to customers. Let's assume that the type of orders is constantly changing, it can be washing machines, refrigerators, microwave ovens.

	def deliver_goods(washing_machine=nil, refrigerator=nil, microwave=nil)
	  puts "Send a washing machine" if washing_machine
	  puts "Send a refrigerator" if refrigerator
	  puts "Send a microwave" if microwave
	end

	deliver_goods("Washing machine model #1421", "Refrigerator model №5223", "Microwave model №2414")
produces:

	Send a washing machine
	Send a refrigerator
	Send a microwave
	=> nil   

But sometimes in your shop the assortment changes and something else appears. For example, suddenly there may be: gas stoves, lawn mowers, and heaters. And what's more, you don't know how to send these goods to a consumer. But someone else knows. For example, your manager. And you have an instruction - all products that go beyond the first three must be sent to your manager for processing. How to show this in a code? Here, the parameter with the splat operator comes to the rescue.

	def deliver_goods(washing_machine=nil, refrigerator=nil, microwave=nil, *another_goods)
	  puts "Send a washing machine" if washing_machine
	  puts "Send a refrigerator" if refrigerator
	  puts "Send a microwave" if microwave
	  puts "Products that must be sent to the another manager: #{another_goods.join(", ")}"
	end

	deliver_goods("Washing machine model №1421", "Refrigerator model №5223", "Microwave model №2414", "Gas oven model №124124",
	"Lawn mowing machine model №2412424", "Heater model №244224")

produces:

	Send a washing machine
	Send a refrigerator
	Send a microwave
	Products that must be sent to the another manager: Gas oven model №124124, Lawn mowing machine model №2412424, Heater model №244224
	=> nil  

Thus, using a parameter with the splat operator, we can generalize all atypical types of arguments that can potentially arise.

**Example 2**
Let's see a simpler example. You decided to feed the wild cats. You don't know how many of them around, you just know that if one comes, then others will come. The operator will solve this problem and you will be able to feed all the cats.

	def feed_the_cats(*cats)
	  cats.each { puts "meow!meow! rrrr rrr" }
	end

	feed_the_cats("cat one", "cat two","cat three","cat four","cat five")

produces:

	meow!meow! rrrr rrr
	meow!meow! rrrr rrr
	meow!meow! rrrr rrr
	meow!meow! rrrr rrr
	meow!meow! rrrr rrr
	=> ["cat one", "cat two", "cat three", "cat four", "cat five"]   


----------
## A parameter with the splat operator can reduce the amount of code
In addition to the fact that we use a parameter with the splat operator when we don't know how many arguments we get, we can also reduce the list of parameters if we know that they are all has the same type. For example, we need to know a serial number of certain products.

The method might look like this:

	def super_deal_serial_numbers(mobile_phone, tablet, laptop, charger, adapter, router)
	  puts mobile_phone.values
	  puts tablet.values
	  puts laptop.values
	  puts charger.values
	  puts adapter.values
	  puts router.values
	end

	super_deal_serial_numbers({samsung: "serial: 14124142"}, {apple: "serial: 1412424124"}, {panasonic: "serial: 124124214"},
	                          {asus: "serial: 364346364346"}, {sony: "serial: 3453223432"}, {acme: "serial: 51251213523553"})
produces:


	serial: 14124142
	serial: 1412424124
	serial: 124124214
	serial: 364346364346
	serial: 3453223432
	serial: 51251213523553
	=> nil

now let's rewrite our code using a parameter with splat operator:

	def super_deal_serial_numbers(*devices)
	  devices.each { |device| puts device.values }
	end

	super_deal_serial_numbers({samsung: "serial: 14124142"}, {apple: "serial: 1412424124"}, {panasonic: "serial: 124124214"},
	                          {asus: "serial: 364346364346"}, {sony: "serial: 3453223432"}, {acme: "serial: 51251213523553"})

produces:

	serial: 14124142
	serial: 1412424124
	serial: 124124214
	serial: 364346364346
	serial: 3453223432
	serial: 51251213523553
	=> [{:samsung=>"serial: 14124142"}, {:apple=>"serial: 1412424124"}, {:panas>"serial: 124124214"}, {:asus=>"serial: 364346364346"}, {:s>"serial: 3453223432"}, {:acme=>"serial: 51251213523553"}]   

As we can see, we were able to achieve the same result as in the first example and the code took up less space for us. You shouldn't use this approach everywhere. In our example, parameter names are not so important. However, if accuracy is important in the transmitted arguments, then positional parameters should be most likely used.


----------
## It is used in constructors when working with collections
For example, we have a class:

	class Component

	  attr_accessor :parts

	  def initialize(parts)
	    @parts = parts
	  end

	  def show_component
	    puts @parts.join(" ,")
	  end

	end

	new_component = Component.new(["part1", "part2"])
	new_component.show_component

produces:

	part1 ,part2
	=> nil   
What's wrong with this code? Firstly, I would like to get rid of the array as an argument and be able to send as many arguments as we want. Secondly, if we will not pass any arguments, we get an error. However, quite often for solving problems we need an instance of a class that is initialized without values and then we assign values to it in the sequence of a solution. Thus, we need to be able to handle cases of calling the new method without arguments.

For example, we can solve the second problem by using a parameter with a default value, which by default will be given an empty array (quite often you can see this in a code, although this is the wrong path).

Our new example:

	class Component

	  attr_accessor :parts

	  def initialize parts=[]
	    @parts = parts
	  end

	  def show_component
	    puts @parts.join(" ,")
	  end

	end

	new_component = Component.new()
	new_component.show_component

	new_component.parts = ["part 1", "part2"]
	new_component.show_component
produces:

	part 1 ,part2
	=> nil
As we see, if we don't pass any arguments, then ruby assigns an empty array to the instance variable inside the constructor. However, this solves only one problem. We still need to pass an array when initializing a new instance of the class. We can solve both problems only with the help of a parameter with splat operator. It automatically converts the entire list of arguments into an array and our code will save other programmers from unnecessary efforts to define an empty array each time after object initialization.

Our code:

	class Component

	  attr_accessor :parts

	  def initialize *parts
	    @parts = parts
	  end

	  def show_component
	    puts @parts.join(" ,")
	  end

	end

	puts "With arguments"
	new_component = Component.new("part 1", "part 2", "part 3")
	new_component.show_component
	puts "Without arguments"
	new_component = Component.new()
	new_component.show_component

produces:

	With arguments
	part 1 ,part 2 ,part 3
	Without arguments

	=> nil   


----------
## In a parameter list, a parameter with the splat operator can be both before and after other parameters
Let's look at several examples with positional parameters:

	def say_something_to_somebody(*people, phrase)
	  people.each { |somebody| puts "#{phrase}, #{somebody}!" }
	end

	say_something_to_somebody("Alex", "John", "Leo", "Wow! It's ruby!")
produces:

	Wow! It's ruby!, Alex!
	Wow! It's ruby!, John!
	Wow! It's ruby!, Leo!
	=> ["Alex", "John", "Leo"]   
As you can see, the first three arguments were assigned to the local variable 'people', and the last to the local variable 'phrase'. Now let's swap parameters and arguments:

	def say_something_to_somebody(phrase, *people)
	  people.each { |somebody| puts "#{phrase}, #{somebody}!" }
	end

	say_something_to_somebody("Wow! It's ruby!", "Alex", "John", "Leo")

produces:

	Wow! It's ruby!, Alex!
	Wow! It's ruby!, John!
	Wow! It's ruby!, Leo!
	=> ["Alex", "John", "Leo"]

A parameter with the splat operator can also be in the center of the parameter list. Ruby will try to understand for which parameters you passed the arguments.

	def say_something_to_somebody(phrase, *people, phrase_two)
	  people.each { |somebody| puts "#{phrase}, #{somebody}!  #{phrase_two}" }
	end

	say_something_to_somebody("Wow! It's ruby!", "Alex", "John", "Leo", "It's cool!")
produces:

	Wow! It's ruby!, Alex!  It's cool!
	Wow! It's ruby!, John!  It's cool!
	Wow! It's ruby!, Leo!  It's cool!
	=> ["Alex", "John", "Leo"]


----------
## Using a parameter with splat operator with a keyword parameter (a keyword parameter should be the last one)
If we define a parameter with the splat operator after a keyword parameter, then we will see an error.

	def say_something_to_somebody(phrase:, *people)
	  people.each { |somebody| puts "#{phrase}, #{somebody}!" }
	end

	say_something_to_somebody(phrase: "Wow! It's ruby!", "Alex", "John", "Leo")
produces:

	(repl):47: syntax error, unexpected ','
	def say_something_to_somebody(phrase:, *people)
	                                      ^
	(repl):49: syntax error, unexpected keyword_end, expecting end-of-input
For some reason, ruby doesn't think that this code is correct. Now, let's swap the arguments and parameters so that the keyword parameter goes last:

	def say_something_to_somebody(*people, phrase:)
	  people.each { |somebody| puts "#{phrase}, #{somebody}!" }
	end

	say_something_to_somebody("Alex", "John", "Leo", phrase: "Wow! It's ruby!")

produces:

	Wow! It's ruby!, Alex!
	Wow! It's ruby!, John!
	Wow! It's ruby!, Leo!
	=> ["Alex", "John", "Leo"]


----------
## Use with parameters with a default value
The parameter with the splat operator must go after a parameter with a default value. But it's better not to use it at all in such combination, it can cause errors. Let's see why. The example that will work correctly:

	def say_something_to_somebody(phrase = "Wooy!", *people, phrase_two)
	  people.each { |somebody| puts "#{phrase}, #{somebody}!  #{phrase_two}" }
	end

	say_something_to_somebody("Wow! It's ruby", "Alex", "John", "Leo", "It's cool!")

produces:

	Wow! It's ruby, Alex!  It's cool!
	Wow! It's ruby, John!  It's cool!
	Wow! It's ruby, Leo!  It's cool!
	=> ["Alex", "John", "Leo"]   

Now we will remove the first argument "Wow! It's ruby" and call the method again:

	def say_something_to_somebody(phrase = "Wooy!", *people, phrase_two)
	  people.each { |somebody| puts "#{phrase}, #{somebody}!  #{phrase_two}" }
	end

	say_something_to_somebody("Alex", "John", "Leo", "It's cool!")

Intuitively, I see this code as follows:

 - Since the phrase parameter has the default value "Wooy!" then
   parameter 'people' must accept the first 3 arguments: "Alex", "John",
   "Leo". And since we have position parameter 'phrase_two', it must
   accept the last argument.

However, if we run the code, we'll see that ruby will act a little differently:

	Alex, John!  It's cool!
	Alex, Leo!  It's cool!
	=> ["John", "Leo"]

As you can see, the first argument was accepted by the phrase parameter, the second argument and the third by the people parameter, and the last one by the phrase_two parameter. But if we swap the parameter with splat operator and the parameter with the default value we will have more problems:

	def say_something_to_somebody(*people, phrase = "Wooy!", phrase_two)
	  people.each { |somebody| puts "#{phrase}, #{somebody}!  #{phrase_two}" }
	end

	say_something_to_somebody("Alex", "John", "Leo", "It's cool!")

produces:

	(repl):1: syntax error, unexpected '=', expecting ')'
	def say_something_to_somebody(*people, phrase = "Wooy!", phrase_two)
	                                               ^
	(repl):1: syntax error, unexpected ',', expecting end-of-input
	def say_something_to_somebody(*people, phrase = "Wooy!", phrase_two)
Thus, a parameter with the splat operator must be after a parameter with a default value.


----------
## Conclusions
- quite often in programming we can find situations where it's necessary to process an indefinite number of arguments where a parameter with splat operator can play a significant role
- a parameter with the splat operator can also be used to reduce the amount of code
- it is used in constructors when working with collections
- in a parameter list, a parameter with the splat operator can be both before and after other parameters
- if we want to use also a keyword parameter, then it must go after a parameter with the splat operator
- a parameter with the splat operator must be after a parameter with a default value

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-2-2" target="_blank">https://www.alexindev.com/posts/parameter-with-splat-operator-in-ruby-part-2-2</a>
