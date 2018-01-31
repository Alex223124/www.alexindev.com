##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/public-methods-in-ruby-part-1-2" target="_blank">https://www.alexindev.com/posts/public-methods-in-ruby-part-1-2</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Public methods in ruby (part 1/2)

In this article, we'll look at the first part of the specifics of how public methods work and how they are used in ruby classes.

----------
This article is divided into the following sections:

 1. [What are public methods in Ruby?](#)
 2. [By default, all methods have public access level](#)
 3. [Exception 1. The initialize method is always private and doesn't require the private method call](#)
 4. [Exception 2. Methods that we define in the global Scope are private methods of the Object class](#)
 5. [Exception 3. Private methods that are defined in the Kernel module](#)
----------
## What are public methods in Ruby?
Public methods are methods that allow objects to interact with each other. The set of public methods of the object creates its interface. The more expressively defined such methods the easier it's to use the object. Public methods haven't restrictions when we using them (they can be used both inside the class and outside the class).

Now let's look at public methods in more detail.


----------
## By default, all methods have public access level.
In order for a method to have the public access level, you do not need to use special methods. By default, all methods are public.

Example:

	class SimpleClass

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

Description:

- We defined the class SimpleClass;
- The class defines 3 instance methods;
- Then, we run each of the methods on the new instance of the class.

result:

	Hi from method_one
	Hi from method_two
	Hi from method_three

If any of these methods had a different level of visibility, then we would see an error.


----------
## Exception 1. The initialize method is always private and doesn't require the private method call.
The second exception is that the initialize method in any class is private and it doesn't require any syntactic constructs for this. Is it defined implicitly or explicitly, doesn't matter.

For instance, we will define a class and try to call the initialize method on an instance of this class:

	class SomeClass
	end

	SomeClass.new.initialize
result:

	private method `initialize' called for #<SomeClass:0x0055858f19f158>
	(repl):5:in `<main>'  
What if I try to make it a public method? We can explicitly define it:

	class SomeClass

	  public

	  def initialize
	    puts "Hello! Now I'm public! Yey!"
	  end

	end

	new_instance = SomeClass.new
	new_instance.initialize

result:


	Hello! Now I'm public! Yey!
	private method `initialize' called for #<SomeClass:0x00562faf42c1f8>
	(repl):12:in `<main>'  

What happened? The first time the method was called because we used the method new which creates a new instance of the class and runs the initialize method for this.

But when we tried to call the method a second time (new_instance.initialize), we got an error that says that the private method was called from outside the class.

Therefore, you can safely say that you can not override the visibility of the initialize method.


----------
## Exception 2. Methods that we define in the global Scope are private methods of the Object class.
There are several exceptions to public methods. The first exception is that the methods defined in the global scope are not public methods. They are defined as private methods in the Object class, after which they are available everywhere (since all new classes inherit from it).

For instance, we will use irb:

	def some_method
	  puts "Hi! I'm private method stored inside Object class"
	end

	some_method

result:

	Hi! I'm private method stored inside Object class
	=> nil

Let's see if the method is added to the Object class. To do this, we add the public method method_with_some_method_inside to the Object class (because outside of the class  we can't use private methods directly):

	def some_method
	  puts "Hi! I'm private method stored inside Object class"
	end

	class Object

	  def method_with_some_method_inside
	    puts "Hello from method_with_some_method_inside!"
	    some_method
	  end

	end

	Object.new.method_with_some_method_inside

result:


	Hello from method_with_some_method_inside!
	Hi! I'm private method stored inside Object class
	=> nil  

And if we will try to call some_method using an explicit receiver, we get an error:

	Object.new.some_method
result:

	private method `some_method' called for #<Object:0x0055fb9d2a2d00>
	(repl):14:in `<main>'  
As we can see, the method some_method was added to the Object class and it's definitely private method.

 **private_methods.include?(:method_name)**
Another way we can check this is to use the private_methods method which will return an array of private methods and then use the include?(method_name) method to check if the name of this method is in the array.


	puts Object.private_methods.include?(:some_method)

	def some_method
	  puts "Hi! I'm private method stored inside Object class"
	end

	puts Object.private_methods.include?(:some_method)

result:

	false
	true
	=> nil   

Good. But why if this is the private method, we can call it directly in the console? We can call private methods only within the objects of the class where these methods are defined and in their subclasses, right? That's right, and this is a slightly in-depth question about how ruby works with context.

When we work in ruby without defining any modules or classes, for example like this:

	puts "1111"
	["aaa", "bbb", "ccc"]
Ruby by default creates an instance of the Object class in which this code serves.

This can be easily verified, let's enlarge one of our previous examples:

	puts "self: #{self}"
	puts "self class: #{self.class}"

	puts "Is the method 'some_method' included in the class Object?"
	puts Object.private_methods.include?(:some_method)

	def some_method
	  puts "Hi! I'm private method stored inside Object class"
	end

	puts "after definition of method 'some_method'?"
	puts Object.private_methods.include?(:some_method)

	some_method

result:

	self: main
	self class: Object
	Is the method 'some_method' included in the class Object?
	false
	after definition of method 'some_method'?
	true
	Hi! I'm private method stored inside Object class
	=> nil   

Thus, when we don't explicitly define the class in which we define methods, ruby does it for us. The same thing happens with another code if you try :).


----------
## Exception 3. Private methods that are defined in the Kernel module.
This feature is rarely mentioned, but the Kernel module has many interesting methods that we use throughout the program and they are all private.  

Let's output them using the private_instance_methods method with false flag.

	Kernel.private_instance_methods(false)
	 => [:sprintf, :format, :Integer, :Float, :String, :Array, :Hash, :catch, :throw, :loop,
	 :block_given?, :trace_var, :untrace_var, :Rational, :at_exit, :JSON, :j, :Complex,
	:set_trace_func, :select, :caller, :caller_locations, :test, :`, :fork, :exit, :sleep,
	:jj, :respond_to_missing?, :load, :gem_original_require, :syscall, :printf, :open, :putc,
	 :print, :readline, :puts, :p, :exit!, :system, :readlines, :gets, :proc, :lambda, :exec,
	 :rand, :srand, :initialize_copy, :initialize_clone, :initialize_dup, :spawn, :abort,
	:trap, :gem, :require, :require_relative, :autoload, :autoload?, :binding,
	:local_variables, :warn, :raise, :fail, :global_variables, :__method__, :__callee__,
	:__dir__, :eval, :iterator?]

As you can see, there are such popular methods as print, puts, rand, raise, etc.

Why is this happening?

It's connected with the work of the Kernel module that is included by class Object from which all objects are inherited in ruby, hence these methods are available to all objects in ruby. And since these methods are private, we can not call them on an explicit receiver. As we see, this exception has much in common with the previous one. '

Let's practice a little and add some method to the Kernel module and then try to call it:

	module Kernel

	  private

	  def double_puts(something)
	    puts something
	    puts something
	  end

	end

Now let's run the double_puts method without initializing an instance of the class and pass "OneOne" string as an argument:

	double_puts("OneOne")

result:

	OneOne
	OneOne
	=> nil
As you can see, we don't need a special method to call double_puts method.


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/public-methods-in-ruby-part-1-2" target="_blank">https://www.alexindev.com/posts/public-methods-in-ruby-part-1-2</a>
