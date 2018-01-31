##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-public-private-and-protected-methods-in-ruby-access-control" target="_blank">https://www.alexindev.com/posts/an-introduction-to-public-private-and-protected-methods-in-ruby-access-control</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## An introduction to public, private and protected methods in Ruby (access control)


In this article, we'll look at the introductory theses on how we can control the visibility of methods in ruby classes.

----------
This article is divided into the following sections:

 1. [Ruby supports 3 levels of access to methods](#)
 2. [How do access control methods work?](#)
 3. [Why do we have these methods in ruby?](#)
 4. [public, private, protected - methods, not modifiers](#)
 5. [public, private and protected works only with methods](#)
----------
## Ruby supports 3 levels of access to methods
Ruby supports 3 levels of access to methods: public, protected, private .

An example of how they can be located in a class:

	class SomeClass

	  def public_method

	    puts "Hi, I'm public method!"

	  end

	  private

	  def private_method

	    puts "Hi! I'm private method!"

	  end

	  protected

	  def protected_method

	    puts "Hi, I'm protected method!"

	  end

	end



----------
## How do access control methods work?
Based on the names of methods you can understand that they are protecting and hiding something :). How does this affect the operation of the program? Let's try to use these methods.

Let's use SomeClass class s and try to call a couple of methods:

	instance = SomeClass.new

	instance.public_method

	instance.private_method

	instance.protected_method
result:

	Hi, I'm public method!

	private method `private_method' called for #<SomeClass:0x0055a1e7d114b8>

	Did you mean?  private_methods

	(repl):24:in `<main>'   

As we see, ruby didn't let us call **private_method** method. Thus,  ruby limits its use. This is the whole function of these delimiters. **If access to the method is limited - the ruby will not allow it to be called**, what motivates us to use other approaches to solve the problem.

Let's extend SomeClass class with other methods:


	class SomeClass

	  def method_which_uses_private_method
	    private_method
	  end

	  def method_which_uses_protected_method
	    protected_method
	  end

	  def public_method
	    puts "Hi, I'm public method!"
	  end

	  private

	  def private_method
	    puts "Hi! I'm private method!"
	  end

	  protected

	  def protected_method
	    puts "Hi, I'm protected method!"
	  end

	end

Let's try to use new methods:

	instance = SomeClass.new

	instance.public_method

	instance.method_which_uses_private_method

	instance.method_which_uses_protected_method

result:


	Hi, I'm public method!

	Hi! I'm private method!

	Hi, I'm protected method!

	 => nil   

As you can see, because methods have a certain visibility, we have to create 2 additional methods and now we can use them.


----------
## Why do we have these methods in ruby?
Why do we have these methods in ruby? Would not it be better if we had no restrictions? Why should a programmer create them?

**The answer to this question - OOP**. When we work with code, our task is to write a solution that can be easily **maintained** and **extended** by other programmers. Through the separation of methods in the zones of visibility we can determine what function is performed by the appropriate methods in the classes.

When a programmer sees methods that have a different level of visibility, he seems to be wearing special glasses.

**Public methods are seen by the programmer as methods that define a class interface** - i.e. these are the methods through which the class is working from outside.

**Private methods are considered as methods that define the internal logic of a class**, these are the methods in which a programmer should not dig after you to understand how to use your class.

Protected methods have several cases of accepting and usually their usage is debatable, but **usually, if a private method can not solve the problem, then the protected method works with the internal logic**. We'll talk about all these types of methods in more detail in the next articles. The main thing, for now, is to understand that the method can have a role in the class and, depending on it, we use one or another access level.


----------
## public, private, protected - methods, not modifiers
Another thing you should understand from the start - levels of access in ruby are provided by methods. public, private, and protected defined inside class called Module as instance methods  (see here: https://ruby-doc.org/core-2.3.1/Module.html). It's all about methods, not modifiers. And if it's methods - then with them you can do practically the same thing as with other methods.


----------
## public, private and protected works only with methods
You can't use them for constants or other kinds of ruby variables.

For example, we'll try to make the local variable private:

	class ExampleClass

	  local_variable_one = "something"

	  private :local_variable_one

	end

result:


	NameError: undefined method `local_variable_one' for class `ExampleClass'

	Did you mean?  local_variables

	        from (irb):5:in `private'

	        from (irb):5:in `<class:ExampleClass>'

	        from (irb):1

	        from /usr/bin/irb:11:in `<main>'

As you can see, all we've got is the error.


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-public-private-and-protected-methods-in-ruby-access-control" target="_blank">https://www.alexindev.com/posts/an-introduction-to-public-private-and-protected-methods-in-ruby-access-control</a>
