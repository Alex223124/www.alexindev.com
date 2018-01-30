##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameters-with-default-values-in-ruby" target="_blank">https://www.alexindev.com/posts/parameters-with-default-values-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Parameters with default values in ruby

In this article, we'll look at the features of working with parameters that have default values.

----------
This article is divided into the following sections:

 1. [The syntax for declaring parameters with default values](#the-syntax-for-declaring-parameters-with-default-values)
 2. [The default values are used only when you don't pass any other arguments](#the-default-values-are-used-only-when-you-dont-pass-any-other-arguments)
 3. [This kind of parameters is useful when we have parameters that most of the time have same values](#this-kind-of-parameters-is-useful-when-we-have-parameters-that-most-of-the-time-have-same-values)
 4. [A sequence of arguments must correspond to a sequence of parameters](#a-sequence-of-arguments-must-correspond-to-a-sequence-of-parameters)
 5. [At the beginning of the list of parameters, we must define parameters that we often change](#at-the-beginning-of-the-list-of-parameters-we-must-define-parameters-that-we-often-change)
 6. [We can use almost any code as default values](#we-can-use-almost-any-code-as-default-values)
 7. [Usage of methods as default values can be useful](#usage-of-methods-as-default-values-can-be-useful)
 8. [Usage of a hash as a default value](#usage-of-a-hash-as-a-default-value)
 9. [Conclusions](#conclusions)

----------
## The syntax for declaring parameters with default values
In order to define a default value for a parameter, we use the equal sign (=) and specify a value to which a local variable inside the method should reference.

For instance:

	def method_name(parameter_one = "default value one", parameter_two = "default value two")
	  puts parameter_one
	  puts parameter_two
	end

	method_name
produces:

	default value one
	default value two
	=> nil  
Thus, parameters with a default value allow us to not pass any arguments and have a working method that works with predefined data.


----------
## The default values are used only when you don't pass any other arguments
The default values will be ignored only if we pass other arguments for the same parameters.

For instance:

	def print_names(name_one = "Alex", name_two = "John", name_three = "Mick")
	  puts name_one
	  puts name_two
	  puts name_three
	end

	print_names

produces:

	Alex
	John
	Mick
	=> nil


----------
## This kind of parameters is useful when we have parameters that most of the time have same values
Let's look at an example in which we will need to send mail to a recipient, but sometimes we also should send mail to a manager and colleagues:

	def send_mail(somebody, carbon_copy_to_manager, carbon_copy_to_colleagues)
	  puts "Send mail to #{somebody}"
	  puts "Send mail to manager" if carbon_copy_to_manager
	  puts "Send mail to collegues" if carbon_copy_to_colleagues
	end

Let's try to send mail to one of the recipients:

	send_mail("John", false, false)
As you can see, the list of arguments doesn't look quite readable.

If most of the letters that we send will be sent only to some individual recipients and only occasionally we will send letters to a manager and colleagues that this form of calling the method seems complicated. Now, let's rewrite the method using parameters that have default values:

	def send_mail(somebody, carbon_copy_to_manager: false, carbon_copy_to_colleagues: false)
	  puts "Send mail to #{somebody}"
	  puts "Send mail to manager" if carbon_copy_to_manager
	  puts "Send mail to collegues" if carbon_copy_to_colleagues
	end

	send_mail("John")
produces:

	Send mail to John
	=> nil    
Thus, we don't need to explicitly specify 'false' each time in the list of arguments, which simplifies the use of the method.


----------
## A sequence of arguments must correspond to a sequence of parameters
When using parameters with a default value, it's important to pay attention to the place that the parameter occupies in the parameter list. If we will swap the arguments when calling the method, we risk overriding the wrong variables.

For instance:

	def print_personal_data(first_name = "Default value", city = "Default value", country = "Default value")
	  puts "First name: #{first_name}"
	  puts "City: #{city}"
	  puts "Country: #{country}"
	end

	print_personal_data("Brazill", "Salvador", "Rodrigo")

produces:

	First name: Brazill
	City: Salvador
	Country: Rodrigo
	=> nil   
As we see, we have mixed everything. Let's call the method more correctly:

	print_personal_data("Brazill", "Salvador", "Rodrigo")
produces:

	First name: Brazill
	City: Salvador
	Country: Rodrigo
	=> nil   


----------
## At the beginning of the list of parameters, we must define parameters that we often change
Since we can't ignore the order in which parameters are defined when the method is called, the first parameters in the parameter list should be those parameters that are most often redefined. Thus, we will not need to transfer the same value each time the method is called.

For instance, we have a method for selling juice. A glass of juice has the same price for the most part of a day (let's imagine that at the last hour of a cafe's work juice costs a little more). Also, when we buy juice in this store, we give a shirt with an illustration of a logo of a company that produces juice. Different people order different amounts of juice, someone orders one glass, someone takes a few glasses for themselves and friends.

Our method:

	def glass_of_juice(cost = 1, gift = "T-shirt", amount = 1)
	  puts "####### order accepted #########"
	  puts cost
	  puts gift
	  puts amount
	  puts "################################"
	end

Let's imagine how the usage of the method will look like:

	glass_of_juice(1, "T-shirt", 2)
	glass_of_juice(1, "T-shirt", 3)
	glass_of_juice(1, "T-shirt", 3)
	glass_of_juice
	glass_of_juice(1.25, "T-shirt", 2)

produces:

	####### order accepted #########
	1
	T-shirt
	2
	################################
	####### order accepted #########
	1
	T-shirt
	3
	################################
	####### order accepted #########
	1
	T-shirt
	3
	################################
	####### order accepted #########
	1
	T-shirt
	1
	################################
	####### order accepted #########
	1.25
	T-shirt
	2
	################################
	=> nil   

As you can see, each time we have to rewrite the default values. But what then is the point of their use? Thus, following our story, the method needs to be rewritten.

To use default values, the method should look like this:

	def glass_of_juice(amount = 1, cost = 1, gift = "T-shirt")
	  puts "####### order accepted #########"
	  puts cost
	  puts gift
	  puts amount
	  puts "################################"
	end
As we see, in this method the order of the parameters was changed. The same method calls will now look like this:


	glass_of_juice(2)
	glass_of_juice(3)
	glass_of_juice(3)
	glass_of_juice
	glass_of_juice(2, 1.25)

produces:

	####### order accepted #########
	1
	T-shirt
	2
	################################
	####### order accepted #########
	1
	T-shirt
	3
	################################
	####### order accepted #########
	1
	T-shirt
	3
	################################
	####### order accepted #########
	1
	T-shirt
	1
	################################
	####### order accepted #########
	1.25
	T-shirt
	2
	################################
	=> nil   

Thus, before using the default values, you should estimate how often a value will change and define in the start of a list of parameters those parameters that will most often change.


----------
## We can use almost any code as default values
When we define default values, then it doesn't mean that it should be objects. We can define an expression. In this case, a local variable inside a method will refer to the result of this expression.

	@phrase = "Hi!"

	def print_args(first_arg = "Hi! I'm first!", second_arg = @phrase + " I'm second!"  )
	  puts first_arg
	  puts second_arg
	end

	print_args
produces:

	Hi! I'm first!
	Hi! I'm second!
	=> nil
We can also use the previous parameter. Periodically in a code you can find a method whose parameters are used by each other.

For instance:

	def some_method(a, b = a + 1)
	  puts a
	  puts b
	end

	some_method(1)

produces:

	1
	2
	=> nil   

It's not recommended approach since it makes code less readable. If you have some kind of behavior then you can create a new method and use it inside a method.


----------
## Usage of methods as default values can be useful
Let's look at an example in which we will use methods as default values.

	class BakeryProducts

	  attr_accessor :milk, :sugar, :eggs, :chocolate

	  def initialize(milk = amout_of_milk, sugar = amout_of_sugar, eggs = amout_of_eggs, chocolate = amout_of_chocolate)
	    @milk = milk
	    @sugar = sugar
	    @eggs = eggs
	    @chocolate = chocolate
	  end

	  def properties
	    puts milk
	    puts sugar
	    puts eggs
	    puts chocolate
	  end

	end

	class Cake < BakeryProducts

	  def amout_of_milk
	    "Some amout of milk for Cake"
	  end

	  def amout_of_sugar
	    "Some amount of sugar for Cake"
	  end

	  def amout_of_eggs
	    "Some amount of eggs for Cake"
	  end

	  def amout_of_chocolate
	    "Some amount of chocolate for Cake"
	  end

	end

	class Pie < BakeryProducts

	  def amout_of_milk
	    "Some amout of milk for Pie"
	  end

	  def amout_of_sugar
	    "Some amount of sugar for Pie"
	  end

	  def amout_of_eggs
	    "Some amount of eggs for Pie"
	  end

	  def amout_of_chocolate
	    "Some amount of chocolate for Pie"
	  end

	end

Description of the code:

 1. We have defined 3 classes;
 2. The BakeryProducts class has the attributes: :milk, :sugar, :eggs, :chocolate;
 3. The Cake and Pie classes are inherited from the BakeryProducts class;
 4. The initialize method, which is defined in the BakeryProducts class has parameters with default values;
 5. As we see from the code, the default values are the names of the methods that are defined in the derived classes;
 6. When we create an instance of the Cake class or the Pie class, the initialize method is automatically called, which computes the default values using the methods of those classes;
 7. Also in the BakeryProducts class, the property method is defined that will allow us to see a value of the attributes.

Let's use our class:

	puts new_cake = Cake.new  
	new_cake.properties
	puts new_pie = Pie.new
	new_pie.properties
produces:

	#<Cake:0x005615dec92350>
	Some amout of milk for Cake
	Some amount of sugar for Cake
	Some amount of eggs for Cake
	Some amount of chocolate for Cake
	#<Pie:0x005615dec90af0>
	Some amout of milk for Pie
	Some amount of sugar for Pie
	Some amount of eggs for Pie
	Some amount of chocolate for Pie
	=> nil   
As we see, in our case, the use of a method call as value can be called useful.


----------
## Usage of a hash as a default value
A quite common pattern for using a parameter with a default value is to use an empty hash. Typically, this approach is used to reduce a list of parameters for a constructor, but it can also be used with other methods if necessary.

For instance, we have a method that has a very long list of parameters:

	class Account

	  def initialize(name, surname, cats_name, language, mobile_phone, favorite_color)
	    @name = name
	    @surname = surname
	    @cats_name = cats_name
	    @language = language
	    @mobile_phone = mobile_phone
	    @favorite_color = favorite_color
	  end

	end

	Account.new("Alex", "InDev", "Mr. Meow", "English", "+111111111111", "Pink")

produces:

	=> #<Account:0x0056491163d2c8 @name="Alex", @surname="InDev", @cats_name="Mr. Meow", @language="English", @mobile_ph"+111111111111", @favorite_color="Pink">  
Now let's rewrite our method, but in this case, we will use a parameter which we will name 'options', which will have a default value in the form of an empty hash:

	class Account

	  def initialize(opti{})
	    @name = options[:name]
	    @surname = options[:surname]
	    @cats_name = options[:cats_name]
	    @language = options[:language]
	    @mobile_phone = options[:mobile_phone]
	    @favorite_color = options[:favorite_color]
	  end

	end

	Account.new(name: "Alex", surname: "InDev", cats_name: "Mr. Meow", language: "English", mobile_phone: "+111111111111", favorite_color:"Pink")

produces:

	=> #<Account:0x005635b4a48bd0 @name="Alex", @surname="InDev", @cats_name="Mr. Meow", @language="English", @mobile_ph"+111111111111", @favorite_color="Pink">   

As we can see, the list of parameters has significantly decreased. Moreover, if we skip somehow one or two arguments, then the variables will refer to nil. Thus, this approach is more useful than using positional parameters, since we have the opportunity not to depend on certain parameters.


----------
## Conclusions
- in ruby we can define a parameter which will have a default value;
- the default values are used only when you don't pass any other arguments;
- this kind of parameters is useful when we have parameters that most of the time have same values;
- a sequence of arguments must correspond to a sequence of parameters;
- at the beginning of the list of parameters, we must define parameters that we often change;
- we can use almost any code as default values;
- usage of methods as default values can be useful.

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameters-with-default-values-in-ruby" target="_blank">https://www.alexindev.com/posts/parameters-with-default-values-in-ruby</a>
