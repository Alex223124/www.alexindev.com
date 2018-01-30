##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/protected-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/protected-methods-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Protected methods in ruby

In this article, we'll look at what protection methods are and how they are used in ruby classes.

----------
This article is divided into the following sections:

 1. [What are protected methods in Ruby?](#)
 2. [Using the protected method](#)
 3. [Using the protected method with explicit arguments](#)
 4. [Using the protected method ("wrapper" syntax)](#)
 5. [Protected methods can't be called outside the class](#)
 6. [Protected methods can be called inside a class inside other methods](#)
 7. [Protected methods are inherited by derived classes](#)
 8. [Protected methods can be called using an explicit receiver](#)
 9. [Why do we need an explicit receiver?](#)
 10. [Example: Creation of comparison methods for instances of the same class](#)
 11. [Example: Definition of the equality comparison operator](#)
 12. [Style recommendations](#)
 13. [Conclusions](#)

----------
## What are protected methods in Ruby?
Protect methods are another kind of methods that exist in ruby. In many languages, this level of access doesn't exist. They are similar to private methods: they can't be called from outside the class but can be called inside the class. The main difference between protected and private methods is that we can specify an explicit receiver for protected methods, which makes it possible for objects of the same type to interact with each other - that is why they are usually used.

Now let's look at protected methods in more detail.


----------
## Using the protected method
The easiest way to define protected methods is to place the protected method before all methods that we want to do protected.

For instance:

	class SimpleClass

	  def use_three_methods
	    method_one
	    method_two
	    method_three
	  end

	  protected

	  def method_one
	    puts "Hello from method_one"
	  end

	  def method_two
	    puts "Hello from method_two"
	  end

	  def method_three
	    puts "Hello from method_three"
	  end

	end

	new_instance = SimpleClass.new
	new_instance.use_three_methods

produces:

	Hello from method_one
	Hello from method_two
	Hello from method_three
	=> nil


----------
## Using the protected method with explicit arguments
We can also use another option for declaring:

	class Example

	  def some_metohd_one
	    some_method_two
	  end

	  def some_method_two
	    puts "Hi from some_method_two"
	  end

	  protected :some_method_two

	end


	new_instance = Example.new
	new_instance.some_metohd_one
produces:

	Hi from some_method_two
	=> nil


----------
## Using the protected method ("wrapper" syntax)
Example:

	class Example

	  def some_metohd_one
	    some_method_two
	  end

	  protected def some_method_two
	    puts "Hi from some_method_two"
	  end

	end

	new_instance = Example.new
	new_instance.some_metohd_one

produces:

	Hi from some_method_two
	=> nil


----------
## Protected methods can't be called outside the class
For instnace, let's try to call a protected method directly:

	class Example

	  def some_metohd_one
	    some_method_two
	  end

	  def some_method_two
	    puts "Hi from some_method_two"
	  end

	  protected :some_method_two

	end


	new_instance = Example.new
	new_instance.some_method_two

produces:

	NoMethodError: protected method `some_method_two' called for #<Example:0x00000000c414b8>
	Did you mean?  some_metohd_one
	        from (irb):17
	        from /usr/bin/irb:11:in `<main>'

As you can see, the error tells us that the method has protected access level, so ruby can't call it.


----------
## Protected methods can be called inside a class inside other methods
The protected methods are similar to the private and public methods so that they can be called inside the methods of the class in which they are defined.

For instance:

	class SomeClass

	  def some_public_method
	    some_method
	  end

	  protected

	  def some_method
	    puts "message from proteted method "
	  end

	end

	new_instance = SomeClass.new
	new_instance.some_public_method

produces:

	message from proteted method
	=> nil


----------
## Protected methods are inherited by derived classes
For instance:


	class FirstClass

	  protected

	  def some_protected_method
	    puts "Message from protected method!"
	  end

	end

	class SecondClass < FirstClass

	  def some_method
	    some_protected_method
	  end

	end

	new_instance = SecondClass.new
	new_instance.some_method

produces:

	Message from protected method!
	=> nil


----------
## Protected methods can be called using an explicit receiver
Example:

	class FirstClass

	  protected

	  def some_protected_method
	    puts "Message from protected method!"
	  end

	end

	class SecondClass < FirstClass

	  def some_method
	    self.some_protected_method
	  end

	end

	new_instance = SecondClass.new
	new_instance.some_method

produces:


	Message from protected method!
	=> nil

Here you should notice that the protected method called on the self object which is explicitly specified. It is this small difference that essentially distinguishes private methods and protected methods.


----------
## Why do we need an explicit receiver?
Some tasks that can't be solved using private methods can be solved using protected methods.

For example, let's create a class in which different instances of the class can exchange money (well, almost exchange :)):

	class Bank

	  def initialize(amount_of_money)
	    @money = amount_of_money
	  end

	  def current_balance
	    "Bank has #{self.money} amount of money."
	  end

	  def grab(another_bank, amount)
	    if another_bank.money > amount
	      self.money = self.money + amount
	      another_bank.money = another_bank.money - amount
	    else
	      puts "They don't have such amount of money"
	    end
	  end

	  private

	  def money
	    @money
	  end

	  def money(money)
	    @money = money
	  end

	end

	bank_one = Bank.new(1000)
	bank_two = Bank.new(1500)
	bank_one.grab(bank_two, 1400)
	puts "First bank balance: #{bank_one.current_balance}"
	puts "Second bank balance #{bank_two.current_balance}"


**Description of the code:**

- The Bank class is defined;

- Inside the class, we defined an initialize method that has the amount_of_money parameter (that is, we can specify the amount of money the bank will have when creating a new instance of the class);

- The class also defines the current_balance method that is needed to better illustrate the example (used at the end of the example). Note that it doesn't have the puts method, it returns the string that uses interpolation;

**method 'grab':**

- The method that is most interesting to us is the grab method. It has two parameters: another_bank and amount;
- The logic of the method - if a bank from which we want to take money doesn't have a certain amount, we will see output about it, the bank will take a certain amount of money and add it to another bank;
- Since the method does not define any logic that involves negotiations it's called 'grab' :).

As private methods, we specified the getter and setter methods for the money property. Typically, these methods have the public access level, but for our example, they need to be private.

Let's run the code and see what happens:

	NoMethodError: private method `money' called for #<Bank:0x00000002275ce8 @m1000>
	        from (irb):68:in `take_money'
	        from (irb):83
	        from /usr/bin/irb:11:in `<main>'

In this example, we used the private method inside the public method. And everything would work - but we used the explicit receiver 'another_bank' in the line 'if another_bank.money> amount'.

Now let's rewrite our example and use protected methods instead of private:


	class Bank

	  def initialize(amount_of_money)
	    # same code
	  end

	  def current_balance
	    # same code
	  end

	  def grab(another_bank, amount)
	    # same code
	  end

	  protected

	  def money
	    @money
	  end

	  def money(money)
	    @money = money
	  end

	end

	bank_one = Bank.new(1000)
	bank_two = Bank.new(1500)
	bank_one.grab(bank_two, 1400)
	puts "First bank balance: #{bank_one.current_balance}"
	puts "Second bank balance: #{bank_two.current_balance}"

produces:


	First bank balance: Bank has 2400 amount of money.
	Second bank balance: Bank has 100 amount of money.
	=> nil

As we see, a very small difference can significantly influence the decision. Thus, the protected level of visibility should be used for methods whose logic is associated with manipulating the states of objects of the same type. Let's look at a few more examples of using the protected methods.


----------
## Example: Creation of comparison methods for instances of the same class
Typically, protected methods are used to create comparison methods between several objects of the same class.

For instace:

	class Thing

	  attr_accessor :size

	  def bigger_then(thing)
	    puts self.compare_with(thing) ? "Yes, you right buddy!" : "No, you wrong."
	  end

	  protected

	  def compare_with(other_thing)
	    self.size > other_thing.size
	  end

	end

Description of the code:

- The Thing class is defined;
- Using the attr_accessor method for class instances, getter and setter methods for the 'size' property are defined;
- The bigger_then method is defined which has 'thing' parameter.
- The logic of the method: it takes an object and then makes a comparison between the instance of the class on which the method was called and the instance of the class that was passed as the argument.
- In the same line, we use the ternary operator. If the expression will return true, then we will see "Yes, you right buddy!" otherwise "No, you wrong.".

Note the use of the puts method, it works with the result of what will return the ternary operator.

Let's create several instances and compare them:

	first_thing = Thing.new
	first_thing.size = 4
	puts "First thing size: #{first_thing.size}"

	second_thing = Thing.new
	second_thing.size = 5
	puts "Second thing size: #{second_thing.size}"

	first_thing.bigger_then(second_thing)

produces:


	First thing size: 4
	Second thing size: 5
	No, you wrong.
	=> nil

What happens if I will swap instances?

	second_thing.bigger_then(first_thing)
produces:

	First thing size: 4
	Second thing size: 5
	"Yes, you right buddy!"  
	=> nil  

As we see our class successfully did the task.


----------
## Example: Definition of the equality comparison operator
Another very common example of using protected methods is to use it with comparison operators. Let's look at the example where we a little extend the behavior of the standard equality comparison operator.

Example:


	class Hero

	  attr_reader :amount_of_wins

	  def initialize(wins)
	    @amount_of_wins = wins
	  end

	  def ==(another_hero)
	    puts self.lvl == another_hero.lvl ? "Hmm... Looks like you right, it's true!" : "Hmmmmm... how you can be soooo wrong?"
	  end

	  protected

	  def lvl
	    if self.amount_of_wins < 10
	      0
	    elsif self.amount_of_wins < 20
	      1
	    elsif self.amount_of_wins < 1000
	      2
	    end
	  end

	end

Now let's try to compare a few instances of the class:

	first_hero = Hero.new(15)
	second_hero = Hero.new(25)
	first_hero == second_hero
produces:

	Hmmmmm... how you can be soooo wrong?
	=> nil
Let's try to use another values for instances of the class:

	first_hero = Hero.new(8)
	second_hero = Hero.new(9)
	first_hero == second_hero
produces:

	Hmm... Looks like you right, it's true!
	=> nil


----------
## Style recommendations
Usually, protected methods are located after the public and private methods of the class.

Example:

	class ExampleClass

	  def method_one
	  end

	  private

	  def method_two
	  end

	  protected

	  def method_three
	  end

	end

Or if we don't have any private methods:


	class ExampleClass

	  def method one
	  end

	  protected

	  def method_two
	  end

	end



----------
## Conclusions
protected methods:
- defined using the protected method;
- inherited to the class derivatives through the inheritance;
- can not be called outside the class directly, only through other methods;
- distinctive feature - we can specify the receiver of the method;
- before using ask yourself: "Will this problem be solved by a private method?"
- it is recommended to place after public and private methods in the class.



----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/protected-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/protected-methods-in-ruby</a>
