##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/anonymous-splat-parameter-in-ruby" target="_blank">https://www.alexindev.com/posts/anonymous-splat-parameter-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Anonymous splat parameter in Ruby

In this article, we'll talk about what is the anonymous splat parameter and how it's commonly used in ruby.

----------
This article is divided into the following sections:

 1. [Syntax](#)
 2. [Used to ignore the arguments that the method will receive](#)
 3. [We can choose which arguments we will ignore](#)
 4. [Used with inheritance and with usage of the super method](#)
 5. [Conclusions](#)

----------
## Syntax
In ruby, we can define a parameter that may not have a name at all - the anonymous splat parameter.

Syntax:

	def method_name(*)
	  # method body
	end


----------
## Used to ignore the arguments that the method will receive
With the help of the anonymous splat parameter, we can ignore the arguments with which a method was called. Let's see an example, for the beginning we will define the anonymous splat parameter and will not pass arguments when the method is called:

	def puts_something(*)
	  puts "Ruby!Ruby!Ruby!"
	end

	puts_something
produces:

	Ruby!Ruby!Ruby!
	=> nil
now let's try to pass something:

	def puts_something(*)
	  puts "Ruby!Ruby!Ruby!"
	end

	puts_something("Phrase 1", "Phrase 2", 111, {hash: "value"})

produces:

	Ruby!Ruby!Ruby!
	=> nil   

As you can see, in both examples we didn't see any error. But if we had a method without parameters and we would pass some arguments, then we would still see the error:

	def puts_something
	  puts "Ruby! Ruby! Ruby!"
	end

	puts_something("first argument", "second argument", "third argument")

produces:

	wrong number of arguments (given 3, expected 0)
	(repl):1:in `puts_something'
	(repl):5:in `<main>'  

Thus, the anonymous splat parameter helps us to ignore arguments that we don't want to use inside a method.


----------
## We can choose which arguments we will ignore
The anonymous splat parameter doesn't affect all arguments. When choosing parameters, we can specify where exactly the arguments will be placed which we don't want to use. Let's look at several options with positional parameters.

In the first example, the anonymous splat parameter is the last parameter in the parameter list:

	def print_phrases(first, second, *)
	  puts first
	  puts second
	end

	print_phrases("First phrase", "Second phrase", "aaa", "bbb", "ccc", "ddd")

produces:

	First phrase
	Second phrase
	=> nil   
Now let's put it first in the list of parameters:

	def print_phrases(*, first, second)
	  puts first
	  puts second
	end

	print_phrases("First phrase", "Second phrase", "aaa", "bbb", "ccc", "ddd")

produces:

	ccc
	ddd
	=> nil   
Now let's put it in the middle of the parameters list:

	def print_phrases(first, *, second)
	  puts first
	  puts second
	end

	print_phrases("First phrase", "Second phrase", "aaa", "bbb", "ccc", "ddd")

produces:

	First phrase
	ddd
	=> nil   

As you can see, the anonymous splat parameter works in a similar way as a parameter with the splat operator, except that we can't use arguments.

Thus, when we use the anonymous splat parameter, we explicitly define for another programmer that some of the arguments with which the method will be called will be unnecessary and will not be used in any way.




----------
## Used with inheritance and with usage of the super method
The anonymous splat parameter is useful when we deal with inheritance and allows us to write more readable code.

For instance, we have a class with a constructor. The constructor has a parameter with the splat operator. At the same time, we have a derived class that also has a constructor. Our goal is to use a method from a derived class and pass arguments to it, but the arguments will be used only by the constructor of the parent class (for this purpose we will use the super method).

Our example:

	class BakeryProducts

	  def initialize(*ingiridients)
	    puts "Lits of ingridients: #{ingiridients.join(", ")}"
	  end

	end

	class Cake < BakeryProducts

	  def initialize(*)
	    puts "We will cook Cakes! Omnomnom!"
	    super
	  end

	end

	Cake.new("milk", "nuts", "eggs", "sugar", "chocolate")

Description of the example:

 1. We defined 2 classes: BakeryProducts and Cake;
 2. The Cake class is inherited from the BakeryProducts class;
 3. The initialize method is explicitly defined in both classes;
 4. In the Cake class we use the method super which calls the initialize method with the same arguments that it was called in the Cake class.

Let's run it:

	We will cook Cakes! Omnomnom!
	Lits of ingridients: milk ,nuts ,eggs ,sugar ,chocolate
	=> #<Cake:0x005642dbbaff58>
Using the anonymous splat parameter we say that the method of the child class doesn't depend on the parameters at all. We extend the behavior of the derived class by using expressions that don't work with arguments (puts "We will cook Cakes! Omnomnom!") and then we call the method from the parent class using the super method. In complex situations, this approach can make the code more readable, since the arguments or part of the arguments can be skipped. Let's complicate our example and determine which Cake we want to make.

Now our example looks like this:

	class BakeryProducts

	  def initialize(*ingiridients)
	    puts "Lits of ingridients: #{ingiridients.join(", ")}"
	  end

	end

	class Cake < BakeryProducts

	  def initialize(name, *)
	    puts "We will cook #{name}! Omnomnom!"
	    super
	  end

	end

	Cake.new("Chocolate Cupcake", "milk", "nuts", "eggs", "sugar", "chocolate")

produces:

	We will cook Chocolate Cupcake! Omnomnom!
	Lits of ingridients: Chocolate Cupcake, milk, nuts, eggs, sugar, chocolate
	=> #<Cake:0x0055953bdfbdc8>

As we can see, the name of the cake - "Chocolate Cupcake" - falls into the list of ingredients. Thus, the anonymous splat operator doesn't solve all the problems.

The method super passed all the arguments that we used when calling the method. To solve the problem, we also need to define the 'name' parameter in the parent class.

Now our example looks like this:

	class BakeryProducts

	  def initialize(name, *ingiridients)
	    puts "Lits of ingridients: #{ingiridients.join(", ")}"
	  end

	end

	class Cake < BakeryProducts

	  def initialize(name,*)
	    puts "We will cook #{name}! Omnomnom!"
	    super
	  end

	end

	Cake.new("Chocolate Cupcake", "milk", "nuts", "eggs", "sugar", "chocolate")

produces:

	We will cook Chocolate Cupcake! Omnomnom!
	Lits of ingridients: milk, nuts, eggs, sugar, chocolate
	=> #<Cake:0x00555cc1fa2bc0>   
As we can see, now everything works as we wanted. Thus, the anonymous splat parameter can help us to write the code in a more readable way if we are dealing with inheritance.


----------
## Conclusions
- the anonymous splat parameter is provided in ruby to ignore all/part of the arguments that we pass to a method;
- ignoring arguments allows us explicitly determine which arguments will not be used in a body of a method;
- the anonymous splat parameter is especially useful when we work with inheritance and usage of the method super.


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/anonymous-splat-parameter-in-ruby" target="_blank">https://www.alexindev.com/posts/anonymous-splat-parameter-in-ruby</a>
