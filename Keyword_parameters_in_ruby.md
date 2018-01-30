##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/keyword-parameters-in-ruby" target="_blank">https://www.alexindev.com/posts/keyword-parameters-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Keyword parameters in ruby

In this article, we'll look at the features of keyword parameters and how they are used in ruby. It should be noted that this type of parameters has other names: named parameters, keyword arguments, required keyword arguments.

----------
This article is divided into the following sections:

 1. [Syntax](#)
 2. [Keyword parameters appeared in version 2.0](#)
 3. [The keywords must be explicitly defined in the argument list when a method is called](#)
 4. [Keyword parameters are mandatory](#)
 5. [When using keyword parameters, arguments from the argument list can be swapped](#)
 6. [Keyword parameters increase the readability of methods](#)
 7. [We can define the default value for keyword parameters](#)
 8. [When we pass new values, we overwrite the default values](#)
 9. [Keyword parameters are ideal for tasks where by default we need to use false](#)
10. [Conclusions](#)

----------
## Syntax
In ruby we can use the keyword parameters.

Let's see how they work:

	class Account

	  def initialize(name:, surname:, language:)
	    @name = name
	    @surname = surname
	    @cats_name = language
	  end

	end

	new_instance = Account.new(name: "Alex", surname: "Sol", language: "English")

produces:

	=> #<Account:0x0055ac8e7eba68 @name="Alex", @surname="Sol", @cats_name="English">

Let's see how our code works:

 1. We defined 3 parameters for the method and passed 3 arguments;
 2. As we see, the arguments are hashes;
 3. At the same time, the parameter syntax looks like a key to a hash.

This is why these parameters are called 'keyword' parameters.


----------
## Keyword parameters appeared in version 2.0
Keyword parameters have appeared in ruby since version 2.0, so if you use previous versions of ruby, this syntax will not work. You can easily check whether your version of ruby supports this syntax. Open irb by typing 'irb' in the terminal and use this code:

	def can_i_use_keyword_parameters?(keyword_parameters:)
	  puts "Yes! You can! "
	end

	can_i_use_keyword_parameters?(keyword_parameters: true)
produces:

	Yes! You can!
	=> nil  
If your version doesn't support this kind of parameters, then you will see an error.


----------
## The keywords must be explicitly defined in the argument list when a method is called
When we use keyword parameters, we must explicitly use the keywords in the argument list. We can try to pass hashes through local variables, but in this case, ruby will not understand what is happening.

Let's see how it looks:

	class Account

	  def initialize(name:, surname:, language:)
	    @name = name
	    @surname = surname
	    @cats_name = language
	  end

	end

	name = {name: "Alex"}
	surname = {surname: "Sol"}
	language = {language: "English"}

	new_instance = Account.new(name, surname, language)

produces:

	wrong number of arguments (given 2, expected 0)
	(repl):3:in `initialize'
	(repl):15:in `new'
	(repl):15:in `<main>'   
At the same time, if we define keywords in the list of arguments, the code will work:

	class Account

	  def initialize(name:, surname:, language:)
	    @name = name
	    @surname = surname
	    @cats_name = language
	  end

	end

	name = {name: "Alex"}
	surname = {surname: "Sol"}
	language = {language: "English"}

	new_instance = Account.new(name: name, surname: surname, language: language)

produces:

	=> #<Account:0x00560ef86ffee8 @name={:name=>"Alex"}, @surname={:surname=>"Sol"}, @cats_name={:language=>"English"}>   
Thus, only when using keywords in the list of arguments we can show ruby that we want to do.


----------
## Keyword parameters are mandatory
Keyword parameters are mandatory. In other words, If we don't pass the corresponding arguments, then ruby will bring us an error. Let's remove one argument from one of the past examples when calling the method:

	class Account

	  def initialize(name:, surname:, language:)
	    @name = name
	    @surname = surname
	    @cats_name = language
	  end

	end

	new_instance = Account.new(name: "Alex", surname: "Sol")

produces:

	missing keyword: language
	(repl):3:in `initialize'
	(repl):11:in `new'
	(repl):11:in `<main>'   
As you can see, the error text directly says what argument we didn't have. In this situation, ruby didn't found the keyword 'language'.


----------
## When using keyword parameters, arguments from the argument list can be swapped
If we use positional parameters, then we often have to remember in which order we need to pass arguments to a method, because from that correctly we will give arguments will depend on what local variables will be assigned the corresponding values. If you are solving a large task that involves many methods, then keeping all the parameters in your head can be an expensive process. Keyword parameters allow you to simplify the task. You can rely on a name of a parameter and change them by places when calling a method, so you only need to know that they are defined by the method.

For instance:

	class Account

	  def initialize(name:, surname:, language:)
	    @name = name
	    @surname = surname
	    @cats_name = language
	  end

	end

	new_instance = Account.new(language: "English", surname: "Sol", name: "Alex")

produces:

	=> #<Account:0x00562219e52d38>   
Notice in which order are the parameters and arguments. They are defined in reverse order to each other.


----------
## Keyword parameters increase the readability of methods

One of the most positive aspects when working with keyword parameters is that they explicitly tell us which necessary arguments should be passed to a method.

For instance:

	class Product

	  def initialize(identificator, serial_muber, messenger_id, in_stock, phone, is_new)
	    @identificator = identificator
	    @serial_muber = serial_muber
	    @messenger_id = messenger_id
	    @in_stock = in_stock
	    @phone = phone
	    @is_new = is_new
	  end

	end

	new_instance = Product.new(11111111, 4124124124124, 124124214, true, 1111111111, true)

produces:

	=> #<Product:0x0055bbc31e7b50 @identificator=11111111, @serial_muber=4124124124124, @messenger_id=124124214, @in_stock=true, @ph1111111111, @is_new=true>

As we see, when we call the method, we have the argument list which is a set of objects that are difficult to understand. We have to pay attention to the corresponding parameter which is determined by the method. From my point of view, we can easily change places of the first three arguments, which will affect the correctness of the data in our program:

	new_instance = Product.new(124124214, 11111111, 4124124124124, true, 1111111111, true)
As a result, we get a product in which the identifier and a serial number of the product are mixed:

	=> #<Product:0x0055d393905350 @identificator=124124214, @serial_muber=11111111, @messenger_id=4124124124124, @in_stock=true, @ph1111111111, @is_new=true>

Now let's replace the positional parameters with the keyword parameters and see how this affects the readability of our code:

	class Product

	  def initialize(identificator:, serial_nuber:, messenger_id:, in_stock:, phone:, is_new:)
	    @identificator = identificator
	    @serial_nuber = serial_nuber
	    @messenger_id = messenger_id
	    @in_stock = in_stock
	    @phone = phone
	    @is_new = is_new
	  end

	end

	new_instance = Product.new(identificator: 11111111, serial_nuber: 4124124124124, messenger_id: 124124214, in_stock: true, phone: +1111111111, is_new: true)

produces:

	=> #<Product:0x0056510ae4d160 @identificator=11111111, @serial_nuber=4124124124124, @messenger_id=124124214, @in_stock=true, @ph1111111111, @is_new=true>

From my point of view, this code is much more readable and it's much more difficult to make a mistake in it than in the first example.


----------
## We can define the default value for keyword parameters
Let's see an example:

class Product

	  def initialize(identificator: "Default value", serial_nuber: "Default value", messenger_id: "Default value", in_stock: false, phone: "Default value", is_new: false)
	    @identificator = identificator
	    @serial_nuber = serial_nuber
	    @messenger_id = messenger_id
	    @in_stock = in_stock
	    @phone = phone
	    @is_new = is_new
	  end

	end

	new_instance = Product.new


produces:

	=> #<Product:0x00564d2db77ca0 @identificator="Default value", @serial_nuber="Default value", @messenger_id="Default value", @in_stock=false, @ph"Default value", @is_new=false>   

We can use any data structure as a default value:

	def print_arguments(string: "This is string" , number: 2222, hash: {key: "value"}, array: [1,2,3,4,5], instance_of_class: Object.new, boolean: true)
	  puts string
	  puts number
	  puts hash
	  puts array
	  puts instance_of_class
	  puts boolean
	end

	print_arguments

produces:

	This is string
	2222
	{:key=>"value"}
	1
	2
	3
	4
	5
	#<Object:0x0055e03af7b6b8>
	true
	=> nil

As we see, despite the fact that we didn't pass a single argument to the method, there was no error.


----------
## When we pass new values, we overwrite the default values
If we want, we can pass new values to the keyword parameters that have default values. In this case, the previous values will be overwritten.

For instance:

	class Product

	  def initialize(identificator: "Default value", serial_nuber: "Default value", messenger_id: "Default value", in_stock: false, phone: "Default value", is_new: false)
	    @identificator = identificator
	    @serial_nuber = serial_nuber
	    @messenger_id = messenger_id
	    @in_stock = in_stock
	    @phone = phone
	    @is_new = is_new
	  end

	end

	new_instance = Product.new(identificator: 11111111, serial_nuber: 4124124124124, messenger_id: 124124214, in_stock: true, phone: +1111111111, is_new: true)

produces:

	=> #<Product:0x0055ead7fe0a48 @identificator=11111111, @serial_nuber=4124124124124, @messenger_id=124124214, @in_stock=true, @ph1111111111, @is_new=true>


----------
## Keyword parameters are ideal for tasks where by default we need to use false
Quite often in ruby we have methods in which we pass the options like true or false. Keyword parameters with default values are ideal for such tasks. For example, we are handling an expedition on Mars and our task is to send messages about our actions. But if we find something very interesting, then we also need to send a message to a space station, a manager of our project, and a department that is on the ground.

Our method:

	def send_message(message, notify_manager: false, notify_station: false, notify_department: false)
	  puts "Send message to computer: #{message}"
	  puts "Send message to manager: #{message}" if notify_manager
	  puts "Send message to station: #{message}" if notify_station
	  puts "Send message to department: #{message}" if notify_department
	end

	send_message("Wow! I found a stone!")

produces:

	Send message to computer: Wow! I found a stone!
	=> nil
For example, we met an alien:

	send_message("Wow! I found an alien! All here!", notify_manager: true, notify_station: true, notify_department: true)

produces:

	Send message to computer: Wow! I found an alien! All here!
	Send message to manager: Wow! I found an alien! All here!
	Send message to station: Wow! I found an alien! All here!
	Send message to department: Wow! I found an alien! All here!
	=> nil   

Thus, this approach allows us to flexibly use the method depending on the arguments that we pass to it.


----------
## Conclusions
- in ruby, there are keywords parameters that allow us to determine what arguments the method will take more explicitly;
- when we pass arguments for the keyword parameters, we must explicitly define the keywords;
- keyword parameters appeared in version 2.0;
- keyword parameters are required;
- it doesn't matter in what order we pass arguments for keyword parameters, the main thing is that there should be a corresponding key;
- we can define the default value for keyword parameters;
- when we pass new values, we overwrite the default values;
- keyword parameters are ideal for tasks where by default we need to use false.

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/keyword-parameters-in-ruby" target="_blank">https://www.alexindev.com/posts/keyword-parameters-in-ruby</a>
