##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/private-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/private-methods-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Private methods in ruby

In this article, we'll look at what private methods are and how they are used in ruby classes.

----------
This article is divided into the following sections:

 1. [What are private methods in Ruby?](#)
 2. [Using the private method](#)
 3. [Using the private method with explicit arguments](#)
 4. [Using the private method ("wrapper" syntax)](#)
 5. [Private methods can't be called outside the class](#)
 6. [Private methods can be called inside a class inside other methods](#)
 7. [Private methods can't be called using an explicit receiver](#)
 8. [Private methods are inherited by derived classes](#)
 9. [Recommendations when to use public methods](#)
 10. [Style recommendations](#)
 11. [Conclusions](#)

----------
## What are private methods in Ruby?

Methods that have private visibility implement the internal logic of the object. They can be called inside the same class in which they are defined, or inside derived classes. Unlike protected methods, you can call them only for a current instance of the class (you can't explicitly specify the method receiver). They are mainly used to conceal the implementation of classes, which positively affects the support of such classes.

Now let's look at private methods in more detail.


----------
## Using the private method.
We can declare private methods using the private method without using explicit arguments:

	For instance:

	class ExampleClass

	  def print_something
	    something
	  end

	  private

	  def something
	    puts "Hi from private method! "
	  end

	end

	new_instance = ExampleClass.new
	new_instance.print_something

produces:

	Hi from private method!
	=> nil


----------
## Using the private method with explicit arguments
Another variant of the syntax of private methods is when we can explicitly pass method names as arguments.

Example:


	class ExampleClass

	  def print_something
	    something
	    some_method
	  end

	  def something
	    puts "Hi from private method! "
	  end

	  def some_method
	    puts "Yes, I'm private too!"
	  end

	  private :something, :some_method

	end

	new_instance = ExampleClass.new
	new_instance.print_something

produces:

	Hi from private method!
	Yes, I'm private too!
	=> nil

If we try to run these methods outside the class, we will see errors:

	new_instance.something

produces:

	NoMethodError: private method `something' called for #<ExampleClass:0x00000002715320>
	        from (irb):85
	        from /usr/bin/irb:11:in `<main>'

and

	new_instance.some_method

produces:

	NoMethodError: private method `some_method' called for #<ExampleClass:0x00000002639ff0>
	        from (irb):112
	        from /usr/bin/irb:11:in `<main>'



----------
## Using the private method ("wrapper" syntax)
An example of how we can "wrap" a method:

	class SomeClass

	  def method_one
	    method_two
	  end

	  private def method_two
	    puts "Hello from private method!"
	  end

	end

	new_instnace = SomeClass.new
	new_instnace.method_one

produces:

	Hello from private method!
	=> nil  



----------
## private methods can't be called outside the class.
This feature separates them from public methods, if we want to call a private method from outside the class, we'll see an error.

Let's create a class with a private method and try to call it from outside the class:

	class Planet

	  private

	  def weight
	    "some value"
	  end

	end

	new_instance = Planet.new
	new_instance.weight

produces:

	NoMethodError: private method `weight' called for #<Planet:0x000000021e24c0>
	        from (irb):30
	        from /usr/bin/irb:11:in `<main>'


----------
## private methods can be called inside a class inside other methods.
Example:

	class SomeClass

	  def method_one
	    method_two
	  end

	  private

	  def method_two
	    puts "I'm private method!"
	  end

	end

	new_instance = SomeClass.new
	new_instance.method_one

produces:

	I'm private method!
	=> nil

Usually, private methods are created just for this use. We have public methods in which a certain work needs to be done and we create one (or a lot) of private methods that this work does.

Private methods can also use each other:

	class SomeClass

	  def method_one
	    method_two
	  end

	  private

	  def method_two
	    puts "I'm private method!"
	    method_three
	  end

	  def method_three
	    puts "Hello from method_three!"
	  end

	end

	new_instance = SomeClass.new
	new_instance.method_one

produces:

	I'm private method!
	Hello from method_three!
	=> nil


----------
## private methods can't be called using an explicit receiver
For instance, let's add self to our example and check:

	class Article

	  def data
	    self.amout_of_pages
	  end

	  private

	  def amout_of_pages
	    puts "Some amount of pages in some #{self.class}"
	  end

	end

	class Book < Article

	end
First, let's try to call the public method called data using an instance of the Article class:

	new_instance = Article.new
	new_instance.data

produces:

	NoMethodError: private method `amout_of_pages' called for #<Article:0x00000002053000>
	        from (irb):34:in `data'
	        from (irb):50
	        from /usr/bin/irb:11:in `<main>'

Can something be changed if we use an instance of the Book class :) ?

	new_instance = Book.new
	new_instance.data

produces:

	NoMethodError: private method `amout_of_pages' called for #<Book:0x000000020443e8>
	        from (irb):34:in `data'
	        from (irb):53
	        from /usr/bin/irb:11:in `<main>'

As you can see, nothing has changed. We have the same error in two cases that tell us that we can't use the private method this way.


----------
## Private methods are inherited by derived classes
In many programming languages, private methods are not inherited, but not in ruby

Example:

	class Article

	  def data
	    amout_of_pages
	  end

	  private

	  def amout_of_pages
	    puts "Some amount of pages in some #{self.class}"
	  end

	end

	class Book < Article

	end

	new_instance = Article.new
	new_instance.data

	new_instance = Book.new
	new_instance.data

produces:

	Some amount of pages in some Article
	Some amount of pages in some Book
	=> nil  

**Description:**

- We defined 2 classes: Article and Book;
- The Book class is inherited from the Article class;
- The class Article defines the method with private access amout_of_pages which uses # {self.class} to output as string a name of the class of the object on which the method is invoked;
- Also in the class I defined the data method that has a public access level that internally calls the amout_of_pages method.

What happened? Both methods were inherited, so we could call the data method which uses the private method amout_of_pages.


----------
## Recommendations when to use private methods

**Private methods are used when we want to hide part of the implementation of a class.**

For instance, we have a class with a set of public methods that we wrote. We know every method and its role. For us, the code looks readable and understandable.

Example:


	class Car

	  def start
	    start_system_one
	    start_system_two
	    start_system_three
	    start_system_four
	    puts "Piiip! Car is ready to go sir, be careful on the road."
	  end

	  def stop
	    stop_system_one
	    stop_system_two
	    stop_system_three
	    stop_system_four
	    puts "Piiip! All functions are stopped. Hope you enjoyed the road"
	  end

	  def start_system_one
	    puts "System one started"
	  end

	  def start_system_two
	    puts "System two started"
	  end

	  def start_system_three
	    puts "System three started"
	  end

	  def start_system_four
	    puts "System four started"
	  end

	  def stop_system_one
	    puts "System one stopped"
	  end

	  def stop_system_two
	    puts "System two stopped"
	  end

	  def stop_system_three
	    puts "System three stopped"
	  end

	  def stop_system_four
	    puts "System four stopped"
	  end

	end


	new_instance = Car.new
	new_instance.start
	new_instance.stop

produces:

	System one started
	System two started
	System three started
	System four started
	Piiip! Car is ready to go sir, be careful on the road.
	System one stopped
	System two stopped
	System three stopped
	System four stopped
	Piiip! All functions are stopped. Hope you enjoyed the road
	=> nil

As far as we can see, the situation is quite simple. We have 2 methods: start and stop (which contain many calls to other methods). Based on the methods name, we understand that these methods are responsible for starting and stopping a machine. Typically this is how the class sees the programmer who made it. Let's add a bit of reality to our example. We will mix the methods in places and add many other functions that will do something.

Changed example:


	class Car

	  def start_system_three
	    run_sub_system_one
	    run_sub_system_two
	    run_sub_system_three
	    puts "System three started"
	  end

	  def stop
	    stop_system_one
	    stop_system_two
	    stop_system_three
	    stop_system_four
	    puts "Piiip! All functions are stopped. Hope you enjoyed the road"
	  end

	  def start_system_one
	    run_sub_system_six
	    run_sub_system_seven
	    run_sub_system_eight
	    stop_sub_system_two
	    puts "System one started"
	  end

	  def start_system_four
	    run_sub_system_three
	    stop_sub_system_one
	    puts "System four started"
	  end


	  def start_system_two
	    run_sub_system_four
	    stop_sub_system_one
	    puts "System two started"
	  end

	  def stop_system_three
	    run_sub_system_one
	    stop_sub_system_five
	    stop_sub_system_four
	    puts "System three stopped"
	  end

	  def start
	    start_system_one
	    start_system_two
	    start_system_three
	    start_system_four
	    puts "Piiip! Car is ready to go sir, be careful on the road."
	  end

	  def stop_system_one
	    stop_sub_system_five
	    stop_sub_system_two
	    puts "System one stopped"
	  end

	  def stop_system_two
	    stop_sub_system_three
	    stop_sub_system_four
	    puts "System two stopped"
	  end

	  def stop_system_four
	    stop_sub_system_six
	    stop_sub_system_seven
	    puts "System four stopped"
	  end

	end


What was changed:
The place of methods in the class has been changed, and the methods run_sub_system and stop_sub_system have been added to cause a little panic for a reader :) Suppose that these methods are defined somewhere and do something with the subsystems of a car.

Now, let's imagine what kind of ideas a programmer will have who sees this class for the first time: "What does start mean?", "What does stop mean?", "What systems?", "In what order should they be run?". The situation turns into a try to manually start each machine system to start a drive. It will be nice to see that someone has already used this class. But you will not be looking at the other driver who starts the ride with a push every time if you can sit down and go at once? Moreover, suppose that some of the methods are named incorrectly and do not lead to any thoughts, the confusion will be even greater. Now, that we are decided not allow such situation to arise again, we will rewrite our example using the private method:

Our new example:


	class Car

	  def start
	    start_system_one
	    start_system_two
	    start_system_three
	    start_system_four
	    puts "Piiip! Car is ready to go sir, be careful on the road."
	  end

	  def stop
	    stop_system_one
	    stop_system_two
	    stop_system_three
	    stop_system_four
	    puts "Piiip! All functions are stopped. Hope you enjoyed the road"
	  end

	  private

	  def start_system_three
	    run_sub_system_one
	    run_sub_system_two
	    run_sub_system_three
	    puts "System three started"
	  end

	  def start_system_one
	    run_sub_system_six
	    run_sub_system_seven
	    run_sub_system_eight
	    stop_sub_system_two
	    puts "System one started"
	  end

	  def start_system_four
	    run_sub_system_three
	    stop_sub_system_one
	    puts "System four started"
	  end


	  def start_system_two
	    run_sub_system_four
	    stop_sub_system_one
	    puts "System two started"
	  end

	  def stop_system_three
	    run_sub_system_one
	    stop_sub_system_five
	    stop_sub_system_four
	    puts "System three stopped"
	  end

	  def stop_system_one
	    stop_sub_system_five
	    stop_sub_system_two
	    puts "System one stopped"
	  end

	  def stop_system_two
	    stop_sub_system_three
	    stop_sub_system_four
	    puts "System two stopped"
	  end

	  def stop_system_four
	    stop_sub_system_six
	    stop_sub_system_seven
	    puts "System four stopped"
	  end

	end

We made a very small change (plus changed the methods in some places in a more logical order), but as we can see, from the outside of the class we can call only 2 methods: start and stop. And the chance that we will try to cause other methods for some reason is reduced. Choose from two methods is much easier than out of ten. Dividing methods by the level of access, we say: "All you need to know to use this class is 2 methods - start and stop".

In addition to problems with the use of classes, there is a common problem with the extension of classes. Imagine that we have many classes and all of them suddenly began to expand. If we regularly modify public methods, then we will have to search for them by the application (in the places where they are called) and replace them with other methods. But if we created a good interface from public methods and everything that can be extended we put in private methods, in that case, we will need to make changes only in the private methods of the corresponding classes and it saves the energy and attention of the programmer.

Hence, implementation is the important thing in classes. As soon as some of the methods come out from our field of vision, they cease to mislead us. All methods that are not used externally from the class, methods that perform work for public methods are best hidden in the private methods.


----------
## Style recommendations
Typically, private methods are placed at the end of the file after public methods. Because other developers will primarily be interested in public methods and therefore there is no need of private methods at the beginning of the file.

Example:

	class ExampleClass

	  def public_method_one
	  end

	  def public_method_two
	  end

	  private

	  def private_method_one
	  end

	  def private_method_two
	  end

	end


----------
## Conclusions
private methods:
- created specifically for storing the implementation of class logic;
- you can declare private methods using method 'private';
- inherited to the class derivatives through the inheritance;
- you can not explicitly specify the recipient of the private method;
- can not be called outside the class directly, only through other methods;
- it is recommended to place after public methods in the class;


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/private-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/private-methods-in-ruby</a>
