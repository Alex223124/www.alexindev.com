##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/public-methods-in-ruby-part-2-2" target="_blank">https://www.alexindev.com/posts/public-methods-in-ruby-part-2-2</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Public methods in ruby (part 2/2)

In this article, we'll look at the second part of the specifics of how public methods work and how they are used in ruby classes.

----------
This article is divided into the following sections:

 1. [When to use the method public?](#)
 2. [Using the public method with explicit arguments](#)
 3. [Using the public method ("wrapper" syntax)](#)
 4. [All public methods can be called from outside the class](#)
 5. [Public methods are available for calling in all other methods](#)
 6. [Public methods are inherited by derived classes](#)
 7. [We can explicitly specify the receiver of the method](#)
 8. [Recommendations when to use public methods](#)
 9. [Style recommendations](#)
 10. [Conclusions](#)

----------
## When to use the method public?

In normal situations, this method will be helpful only for readability. Keep in mind that most style guides don't recommend using this method, it is assumed that everyone knows that methods have public access level by default. If you worked with a language with strict requirements for the presence of a pointer - change your habit. You can be forgiven for using this method, but be aware that this will not last forever :).

Let's use one of the previous examples and add the call of the public method inside of the class before all methods:

	class SimpleClass

	  public

	  def method_one
	    puts "Hi from method_one"
	  end

	  def method_two
	    puts "Hi from method_two"
	  end

	  def method_three
	    puts "Hi from method_three"
	  end

	end

	new_instance = SimpleClass.new
	new_instance.method_one
	new_instance.method_two
	new_instance.method_three

result:

	Hi from method_one
	Hi from method_two
	Hi from method_three
	=> nil  

As we see, nothing has changed. Therefore, the public method is not usually used, why spend time on it?


----------
## Using the public method with explicit arguments.
Another way to define public methods is to use the public method, with explicitly passed method names to it as arguments.

For instance:

	class SomeClass

	  def some_method_one
	    puts "111"
	  end

	  def some_method_two
	    puts "222"
	  end

	  public :some_method_one, :some_method_two

	end

	new_instance = SomeClass.new
	new_instance.some_method_one
	new_instance.some_method_two

result:

	111
	222
	=> nil  

It's difficult to say when such syntax is needed, but since it exists - welcome! :)


----------
## Using the public method ("wrapper" syntax).
Another option to determine the visibility of the method:

	class ExampleClass

	  public def test_method_one
	    puts "Hi from test_method_one!"
	  end

	  public def test_metohd_two
	    puts "Hi from test_method_two!"
	  end

	end

This approach has an important advantage. If you have a lot of methods in the class and you see a lot of public, private and protected methods, it will inspire uncertainty when placing the method in a file: "What access level will the method have if I add it here? Do I need to run through the entire file and analyze this?".  And in this case, you see how the access level is immediately specified: "If there is something there it will be rewritten, I can move on."


----------
## All public methods can be called from outside the class.
It's worthwhile to pay attention to this. When public methods are defined in the class, we can create an instance of the class and call this method outside the class (for comparison, you can't do this with private methods because you will see the error).

For instance:

	class SimpleClass

	  def method_one
	    puts "Hi from method_one"
	  end

	end

	new_instance = SimpleClass.new
	new_instance.method_one

result:


	Hi from method_one
	=> nil  

As you can see, the method was called and returned us the message.


----------
## Public methods are available for calling in all other methods.
In addition to the fact that they can be called from outside the class, we can also call them inside the class in other methods (inside private, protected, and other public methods).

For instance, we will create a class with 3 public methods and make the methods call each other. Typically, if a method is used inside a public method, it implements the internal logic of the object, but sometimes it doesn't (like in our example).

Example:

	class Creature

	  def chaotic_movements
	    rand(1..4).times { move_left }
	    rand(1..4).times { move_right }
	    rand(1..4).times { move_back }
	    rand(1..4).times { move_forward }
	  end

	  def move_left
	    puts "Left!"
	  end

	  def move_right
	    puts "Right!"
	  end

	  def move_back
	    puts "Back!"
	  end

	  def move_forward
	    puts "Back!"
	  end

	end

	new_creature = Creature.new
	new_creature.move_left
	new_creature.move_back
	new_creature.move_forward
	new_creature.move_right
	new_creature.chaotic_movements


Description of the code:

- We have class Creature;
- The class defines 5 public methods;
- In the chaotic_movements method, we use the rand method whose task is to output the arbitrary number so that we can use the times method to call the code which is in the block { } a certain number of times.

result:

	Left!
	Back!
	Forward!
	Right!
	Left!
	Left!
	Right!
	Right!
	Right!
	Back!
	Forward!
	Forward!
	Forward!
	Forward!
	=> 4  

You can play a little with this example, each time at the end of the script there will be different actions.

Let's add to our example 2 methods, one private and one protected:


	class Creature

	  def chaotic_movements
	    rand(1..4).times { move_left }
	    rand(1..4).times { move_right }
	    rand(1..4).times { move_back }
	    rand(1..4).times { move_forward }
	  end

	  def move_left
	    puts "Left!"
	  end

	  def move_right
	    puts "Right!"
	  end

	  def move_back
	    puts "Back!"
	  end

	  def move_forward
	    puts "Forward!"
	  end

	  def slip_away
	    jump
	    hide
	  end

	  private

	  def jump
	    move_left
	    move_right
	    puts "Jump! Jump! Jump!"
	  end

	  protected

	  def hide
	    move_left
	    move_back
	    puts "Hide!"
	  end

	end

	new_creature = Creature.new
	new_creature.slip_away


result:

	Left!
	Right!
	Jump! Jump! Jump!
	Left!
	Back!
	Hide!
	=> nil  

As you can see, public methods have been successfully called.


----------
## Public methods are inherited by derived classes.
For instance:


	class GuessWhoOne

	  def say_something
	    puts "Meow!"
	  end

	end

	class GuessWhoTwo < GuessWhoOne
	end

	class GuessWhoThree < GuessWhoTwo
	end


	new_instance = GuessWhoOne.new
	new_instance.say_something

	second_new_instance = GuessWhoTwo.new
	second_new_instance.say_something

	third_new_instance = GuessWhoThree.new
	third_new_instance.say_something

Description:

- We defined several classes, in one of the classes the public method say_set is defined.
- Classes are inherited from each other.
- Then we call the say_something method on the instances of the corresponding classes.


result:

	Meow!
	Meow!
	Meow!
	=> nil  
As you can see, we defined the method once and it is available in all child classes.


----------
## We can explicitly specify the receiver of the method.
For this example, we'll look at a fairly large class, but after that, you will see a description :).

Example:

	class Flag

	  attr_accessor :number, :position

	  def initialize(number, position)
	    @number = number
	    @position = position
	    puts "Initial position of flag number #{number}: #{position}"
	  end

	  def swap_with(other_flag)
	    first_flag_position = self.position
	    second_flag_position = other_flag.position

	    self.position = second_flag_position
	    other_flag.position = first_flag_position

	    puts "Positions of flags number #{self.number} and #{other_flag.number} changed successfully"
	  end

	  def self.current_position_of_flags(*flags)
	    flags.each do |flag|
	      puts "Position of flag nubmer #{flag.number}: #{flag.position}"
	    end
	  end

	end

	new_flag_one = Flag.new(1, "first")
	new_flag_one.position = 1

	new_flag_two = Flag.new(2, "second")
	new_flag_two.position = 2

	# swap them
	new_flag_one.swap_with(new_flag_two)
	Flag.current_position_of_flags(new_flag_one, new_flag_two)

	# swap them back
	new_flag_one.swap_with(new_flag_two)
	Flag.current_position_of_flags(new_flag_one, new_flag_two)

**Code description:**

- We defined class Flag. Main purpose of this class is to control the logic of the flags (suppose that flags are on some sporting event).
- The class defines getters and setters for the number and position properties using the attr_accessor method;

**initialize method:**

- The initialize method is defined (when we call Flag.new we can pass 2 arguments, number and position, which will display the state of the object);
- The last line in the initialize method uses the puts method to print a string to the console;
- Inside the string we use interpolation # {...}  whose task is to display the contents of the variables number and position;

**swap_with method:**

- The swap_with method has 1 parameter - other_flag;
- The purpose of the method is to swap flags positions;
- Inside the method, the first thing we do is assign the current positions of the two flags to the corresponding local variables, then we redefine the `position` property of the objects themselves;
- The first object is assigned the position of the second, the second is assigned the position of the first, then we display the message which indicate that the exchange was successful;
- Inside the method, I added empty lines to make it easier to understand: the first part - assign variables, the second part - change places, the third part - output the line to the console.

**current_position_of_flags method:**

- In contrast to other methods, this method is the class method;
- The purpose of this method is to output messages about where each flag is located (the method is added to the example for better understanding of what is happening);
- Why class method? It serves a group of instances of the class;
- In the parameters was specified splat operator which indicates that the method can accept an undefined number of arguments.
- Inside the method, the iterator is used to run through each object and display the message about the number of the flag and its position.

result:

	Initial position of flag number 1: first
	Initial position of flag number 2: second
	Positions of flags number 1 and 2 changed successfully
	Position of flag nubmer 1: 2
	Position of flag nubmer 2: 1
	Positions of flags number 1 and 2 changed successfully
	Position of flag nubmer 1: 1
	Position of flag nubmer 2: 2
	=> [#<Flag:0x005631960e58d0 @number=1, @position=1>, #<Flag:0x005631960e5380 @number=2, @position=2>]  
As you can see, the code works :).

What does this example illustrate? There are many situations when we use public methods with an explicit receiver. The most common methods that we use in this way are getters and setters. We actively used them inside the swap_with method and if they had private visibility we wouldn't be able to use them that way.


----------
## Recommendations when to use public methods
Public methods are used to represent the class interface. The general recommendation is that the less unnecessary methods there are, the better. The more readable the public methods will look, the easier it is to use this class and understand what kind of entity we have. Having completed the work on the class, imagine that you see it the first time, how many possible mistakes will you make when you use it? Will this class be understandable for a beginner programmer?


----------
## Style recommendations
Because public methods play an important role - they define the interface of a class, then it is logical to assume that these are the methods that the programmer must see first. I suppose that's why most style guides recommend placing public methods at the beginning of the class (and not only in the ruby). Without understanding the interface of the class, you rarely want to look for something in private and protected methods.

For instance, we have a class:

	class TaskManager

	  def do_tasks(a,b,c)
	    sub_process_one(a)
	    sub_process_two(b)
	    sub_process_three(c)
	  end

	  private

	  # some private methods

	end

Until I have questions about how the do_tasks method works, I most likely will not look for private methods. And for usage, it should be enough to know the name of the method and the list of its parameters.


----------
## Conclusions
public methods:
- created specifically for creating a class interface;
- by default, all methods are public, but there are a few exceptions;
- you can use the public method to declare, but only if you really need it;
- public methods can be called from outside the class as well as inside the class;
- you can explicitly specify receiver of the method;
- inherited to the class derivatives through the inheritance, they don't lose their 'abilities' in the derived classes;
- the better you define public methods, the more number of programmers will be happy to use your classes to solve their problems;
- usually placed first in the class, before private and protected methods.


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/public-methods-in-ruby-part-2-2" target="_blank">https://www.alexindev.com/posts/public-methods-in-ruby-part-2-2</a>
