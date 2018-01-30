##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/common-notes-about-public-private-and-protected-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/common-notes-about-public-private-and-protected-methods-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Common notes about public, private and protected methods in ruby

Here I decided to post everything that in my opinion did not fit with other things from other articles about public, private and protected methods.

----------
This article is divided into the following sections:

 1. [Private and protected methods are accessible via the send method](#)
 2. [When is the send method usually used?](#)
 3. [Private and protected methods are not accessible through the public_send method](#)
 4. [The send method and whether there is the access level in ruby](#)
 5. [The visibility of the method can be redefined just like the method itself](#)
 6. [We can change the visibility of the method in derived classes too](#)
 7. [How to list private, public, protected methods of the class](#)
 8. [Dinosaurs and Excalibur (example of using public, private, and protected methods together](#)
 9. [Conclusions](#conclusions)
----------
## Private and protected methods are accessible via the send method

Protected and private methods can be called from anywhere by using the send method. This method ignores the access level. This is why it is usually forbidden for use in projects.

The syntax for using the send method is:

	object.send(:method_name)
or

	object.send("method_name")

For instance, let's define several methods and use send to call them:

	class SomeClass

	  def public_method
	    puts "Hello from public method!"
	  end

	  private

	  def private_method
	    puts "Hello from private method!"
	  end

	  protected

	  def protected_method
	    puts "Hello from protected method!"
	  end

	end


	new_instance = SomeClass.new
	new_instance.send(:private_method)
	new_instance.send(:protected_method)
produces:

	Hello from private method!
	Hello from protected method!
	=> nil  

As we can see, the send method overcame the visibility of the methods and called them.

For comparison, if you call these methods without using the send method, then we will see the corresponding errors:

	new_instance = SomeClass.new
	new_instance.private_method

produces:

	NoMethodError: private method `private_method' called for #<SomeClass:0x0000000170b448>
	Did you mean?  private_methods
		from (irb):22
		from /usr/bin/irb:11:in `<main

and

	new_instance = SomeClass.new
	new_instance.protected_method

produces:

	NoMethodError: protected method `protected_method' called for #<SomeClass:0x00000000a94390>
	Did you mean?  protected_methods
		from (irb):21
		from /usr/bin/irb:11:in `<main>'


----------
## When is the send method usually used?
Usually it is very useful for testing. Imagine that your private or protected method is used in some method and you can't run it directly. The send method gives you the ability to use the method in isolation and make sure that it performs its function. But there are no cases when you should use the send method when solving ordinary tasks, the whole idea of setting access to methods (interface, implementation) disappears if you use the send method on the regular basis. If you can't solve a problem without using the send method, then possibly you are dealing with a code that is poorly written and maybe it should be rewritten.


----------
## Private and protected methods are not accessible through the public_send method
It should be clarified that in ruby there is a similar method public_send which works a little differently then the send method. The public_send method knows that OOP is good and If you will use it with private or protected methods then an error will be thrown. Let's use the class from the previous example and call the same methods with public_send:

	new_instance = SomeClass.new
	new_instance.public_send(:private_method)

produces:

	NoMethodError: private method `private_method' called for #<SomeClass:0x0000000244e1a0>
	Did you mean?  private_methods
	        from (irb):182:in `public_send'
	        from (irb):182
	        from /usr/bin/irb:11:in `<main>'

Let's try to call the protected method:

	new_instance.public_send(:protected_method)

produces:

	NoMethodError: protected method `protected_method' called for #<SomeClass:0x0000000244e1a0>
	Did you mean?  protected_methods
	        from (irb):183:in `public_send'
	        from (irb):183
	        from /usr/bin/irb:11:in `<main>'


----------
## The send method and whether there is the access level in ruby.
A bit of my opinion :)

Since in the language the opportunity to overcome the visibility limitations is realized, there is an opinion that in fact there are no private and protected methods in ruby. I think this statement is partly correct since we have such ability. But do we use the send method on regular basis? That's the whole question. And I guess that it is necessary to have unlimited possibilities in the programming language so that we can use them in critical situations.

For example, imagine that a bug was discovered that is related to the visibility of methods, the application is used by a lot of people and it should perform its functions (or more critical - some commercial functions). The fastest way to solve this problem is to use the send method in a piece of code where a mistake was made during programming. Which is better, to have 'iron' access identifiers or to be able to overcome these restrictions? Since the task of any technology is to be useful, languages with strong constraints may require more time to solve problems, which may have an effect in the form of lost profits. Therefore, I see this method as an opportunity to solve the situation if it's worth it.


----------
## The visibility of the method can be redefined just like the method itself
If you use the same method with different access levels, then the last method with the last defined access level will work. Why is this important? In ruby on rails projects, you can find large models (the model is a class with methods which has different access levels) that can reach several hundred lines and sometimes it's difficult to understand what is happening, especially if some methods have been rewritten and the old ones have not been deleted.

Knowing that the level of access can be overwritten will help you save your time. In addition, this is the excellent case when 'public: some_method' can provide effective help in problem search.

For instance:

	class ExampleClass

	  def some_method
	    puts "Hello!"
	  end

	  private :some_method
	  protected :some_method
	  public :some_method

	end


Description of the code:

 1. The ExampleClass class is defined;
 2. One method has been defined 'some_method' which prints the string "Hello!";
 3. Next, we define the access level for the method and do this 3 times (which is't necessary for normal situations, but we assume that the first 2 visibility definitions were skipped by an inattentive programmer)

Let's test what level of access this method has:

Is the method available from the outside?

	new_instance = ExampleClass.new
	new_instance.some_method

produces:

	Hello!
	=> nil  
Of all 3 types of access, only methods with a public access level can be called from outside.

But what if we will define the same method 3 times and give all 3 methods a different level?
The answer - the result will be the same.

For instance:

	class ExampleClass

	  def some_method
	    puts "Hello 1!"
	  end

	  private

	  def some_method
	    puts "Hello 2!"
	  end

	  protected

	  def some_method
	    puts "Hello 3!"
	  end

	end
Let's try to call as a public method:

	new_instance = ExampleClass.new
	new_instance.some_method

produces:

	protected method `some_method' called for #<ExampleClass:0x005611b50d4ce8>
	(repl):23:in `<main>'

As we see from the error text, the method has the protected access level. We tried to call the protected method from outside the class, which can't be done with protected methods.

Let's see what happened step by step:

 1. We defined the first method 'some_method' which had public access (because by default methods have the public access level);
 2. Then the interpreter saw the private method which, as arguments, implicitly accepts all the methods that are written below it in the class (until a next method that determines the access level of the other methods);
 3. Then the interpreter saw another method, 'some_method' (in the situation when the interpreter sees 2 methods with the same name, he recognizes the last of the methods, that is, the method that was last determined when the program was read by the interpreter);
 4. Then the interpreter saw the protected method and the some_method method, the last variant of the some_method method became the latest version of this method, i.e. it became relevant and interpolator used it when we called this method in 'new_instance.some_method' and since its name was accepted as an argument for the protected method, this method has a protected access level. Accordingly, we received an error.

Let's add a child class and some method for testing.

Now our example looks like this:


	class ExampleClass

	  def some_method
	    puts "Hello 1!"
	  end

	  private

	  def some_method
	    puts "Hello 2!"
	  end

	  protected

	  def some_method
	    puts "Hello 3!"
	  end

	end

	class ChildClass < ExampleClass

	  def use_protected_method
	    some_method
	  end

	end

	new_instance = ChildClass.new
	new_instance.use_protected_method

produces:

	Hello 3!
	=> nil  
As we see, the method was called, everything is correct.


----------
## We can change the visibility of the method in derived classes too
Most likely, you will not use this approach to solve problems, but when you will look for errors knowing this thing can be very useful.

Example:

	class SomeClass

	  def method_one
	    puts "Hi!, I'm method one"
	  end

	  def method_two
	    puts "Hi, I'm method two"
	  end

	end

	class ChildClass < SomeClass

	  private :method_one
	  private :method_two

	end
If we will call methods on the SomeClass instance, everything work:

	new_instace = SomeClass.new
	new_instace.method_one
	new_instace.method_two

produces:

	Hi!, I'm method one
	Hi, I'm method two
	=> nil  

But if we will call the same methods on an instance of the ChildClass class, we will see NoMethodError error:

	new_child_class_instance = ChildClass.new
	new_child_class_instance.method_one
	new_child_class_instance.method_two
produces:

	private method `method_one' called for #<ChildClass:0x00558efbf489d8>
	Did you mean?  method
	(repl):22:in `<main>'  

What happened? The visibility of the methods was rearranged, now in the derived class they have private visibility level.


----------
## How to list private, public, protected methods of the class
The syntax of ruby is rich in methods of reflection that helps in understanding of written code. The Module class defines several methods that can help us in estimating how many and which methods are in the class. We are talking about methods such: private_instance_methods, protected_instance_methods, public_instance_methods. Calling them we get an array of methods names that have public, private or protected visibility in the class. These methods are implemented as class methods, i.e. we don't need to initialize an instance of the class to use them, we can call them directly on the class.

Let's create a class and call each of these methods. For our example, we will also pass `false` argument to each method, which according to the documentation means that the method will only work with the current class, without taking into account the methods that it inherits from the other classes. We also use the puts method and interpolation.

Example:

	class ExampleClass

	  def public_method_one
	  end

	  def public_method_two
	  end

	  private

	  def private_method_one
	  end

	  protected

	  def protected_method_one
	  end

	end


	puts "Public methods: #{ExampleClass.public_instance_methods(false)}"
	puts "Private methods: #{ExampleClass.private_instance_methods(false)}"
	puts "Protected methods: #{ExampleClass.protected_instance_methods(false)}"
produces:

	Public methods: [:public_method_one, :public_method_two]
	Private methods: [:private_method_one]
	Protected methods: [:protected_method_one]
	=> nil  

As you can see, now we can in a convenient form get a picture of what is the interface of the class, and what is its implementation.

The use of this method can be especially helpful if methods with different access levels are not structured in the class.


----------
## Dinosaurs and Excalibur (example of using public, private, and protected methods together)


Sometimes it's difficult to come up with a suitable example for certain features of the language. The purpose of this example is to show how all three types of methods are related to each other.

**Description of the example:**

Imagine that we have dinosaurs, every dinosaur has a house, short paws and a ball for football (not every day you can see such dinosaurs, but those who saw them know that it's all true :)). One of the dinosaurs has a sword, and it's not just a sword, its Excalibur!. Because one wizard decided to give it to a dinosaur for storage.

Dinosaurs are cute creatures and they take great care of those things that are given to them. Moreover, dinosaurs have a notable side -  modesty. They don't tell other kinds of creatures about themselves, so as not to embarrass the other kinds of creatures.

Let's write a class that will correspond to our unusual story:


	class Dinosaur

	  DIGNITIES = ["I know 20 ways how to cook people!",
	               "I know how to growl!",
	               "I know how to jump!"]

	  attr_accessor :name, :has_ecscalibur

	  def initialize(name, has_ecscalibur = false)
	    @name = name
	    @has_ecscalibur = has_ecscalibur
	    puts "Dinosaur #{name}: Yiiiii"
	  end

	  def tell_about_your_friend(other_dinosaur)
	    puts "#{self.name}: His name is #{other_dinosaur.name} and some time ago he told me: '#{other_dinosaur.tell_your_dignities}'. What a great buddy!"
	  end

	  def check_if_ecscalibur_here
	    puts has_ecscalibur? ? "#{self.name}: Yes! It's here!" : "#{self.name}: Hm.. looks like I havent such thing.."
	  end

	  protected

	  def tell_your_dignities
	    DIGNITIES.sample
	  end

	  private

	  def has_ecscalibur?
	    @has_ecscalibur
	  end

	end

Description of the code:

1) The example defines the class Dinosaur;

2) For objects, getters and setters methods are defined for properties 'name' and 'has_ecscalibur';

3) The initialize method is defined, which takes 2 parameters, one of which has a default value of false (because Excalibur exists in one instance);

4) Defined private method  'has_ecscalibur?' which should return true or false if the dinosaur has the sword;

5) Defined protected method 'tell_your_dignities' whose task is to return a string in which the dignity of a certain dinosaur will be described;

6) To output a string we use the constant DIGNITIES, inside which we have 3 values. Maybe dinosaurs have more dignities, but for our purposes, 3 is pretty enough.  In order to get a random object from an array, we use 'sample' method;

7) Also in the example, we have the tell_about_your_friend method, which takes the parameter other_dinosaur and then inside the method we use an instance of the class to call the name method (other_dinosaur.name), and call the protected method tell_your_dignities;

8) The check_if_ecscalibur_here method is defined which, depending on what the has_ecscalibur? method returns will output one string (we use the ternary operator for this).


Let's create a few dinosaurs:


	first_dinosaur = Dinosaur.new("Anthony")
	second_dinosaur = Dinosaur.new("Rory")
produces:


	Dinosaur Anthony: Yiiiii
	Dinosaur Rory: Yiiiii
	=> #<Dinosaur:0x00560b7ff0ed40 @name="Rory", @has_ecscalibur=false>

Let the wizard hand the sword to one of the dinosaurs in our example.

To do this, we will define the Wizard class with the give_sword method which will use the sample method which will emulate the wizard's selection and the has_ecscalibur setter.

Note that this method has a parameter that has a splat operator, which means that we can pass an undefined number of arguments to the method and inside the method they will all be available in one array.

Our new class:

	class Wizard

	  def give_sword(*creatures)
	    creatures.sample.has_ecscalibur = true  
	    puts "Wind: 'Wizard decided to give magick sword to one of the dinosaurs!!'"
	  end

	end

	Wizard.new.give_sword(first_dinosaur,second_dinosaur)

produces:

	Wind: 'Wizard decided to give magick sword to one of the dinosaurs!!'
	=> nil  

Now let's check which dinosaurs have the sword:

	first_dinosaur.check_if_ecscalibur_here
	second_dinosaur.check_if_ecscalibur_here
produces:

	Anthony: Yes! It's here!
	Rory: Hm.. looks like I havent such thing..
	=> nil

Let's ask the dinosaurs to tell us about themselves:

	first_dinosaur.tell_about_your_friend(second_dinosaur)
	second_dinosaur.tell_about_your_friend(first_dinosaur)
produces:

	Anthony: His name is Rory and some time ago he told me: 'I know how to jump!'. What a great buddy!
	Rory: His name is Anthony and some time ago he told me: 'I know 20 ways how to cook people!'. What a great buddy!
	=> nil  

As you can see, in our example there are public, private and protected methods. Every method has its own area of responsibility and they are perfectly combined (from my point of view).

**The complete example:**


	class Dinosaur

	  DIGNITIES = ["I know 20 ways how to cook people!",
	               "I know how to growl!",
	               "I know how to jump!"]

	  attr_accessor :name, :has_ecscalibur

	  def initialize(name, has_ecscalibur = false)
	    @name = name
	    @has_ecscalibur = has_ecscalibur
	    puts "Dinosaur #{name}: Yiiiii"
	  end

	  def tell_about_your_friend(other_dinosaur)
	    puts "#{self.name}: His name is #{other_dinosaur.name} and some time ago he told me: '#{other_dinosaur.tell_your_dignities}'. What a great buddy!"
	  end

	  def check_if_ecscalibur_here
	    puts has_ecscalibur? ? "#{self.name}: Yes! It's here!" : "#{self.name}: Hm.. looks like I havent such thing.."
	  end

	  protected

	  def tell_your_dignities
	    DIGNITIES.sample
	  end

	  private

	  def has_ecscalibur?
	    @has_ecscalibur
	  end

	end

	class Wizard

	  def give_sword(*creatures)
	    creatures.sample.has_ecscalibur = true  
	    puts "Wind: 'Wizard decided to give magick sword to one of the dinosaurs!!'"
	  end

	end

	first_dinosaur = Dinosaur.new("Anthony")
	second_dinosaur = Dinosaur.new("Rory")

	Wizard.new.give_sword(first_dinosaur,second_dinosaur)

	first_dinosaur.check_if_ecscalibur_here
	second_dinosaur.check_if_ecscalibur_here

	first_dinosaur.tell_about_your_friend(second_dinosaur)
	second_dinosaur.tell_about_your_friend(first_dinosaur)

produces:

	Dinosaur Anthony: Yiiiii
	Dinosaur Rory: Yiiiii
	Wind: 'Wizard decided to give magick sword to one of the dinosaurs!!'
	Anthony: Yes! It's here!
	Rory: Hm.. looks like I havent such thing..
	Anthony: His name is Rory and some time ago he told me: 'I know how to jump!'. What a great buddy!
	Rory: His name is Anthony and some time ago he told me: 'I know how to jump!'. What a great buddy!
	=> nil   


----------
## Conclusions
- you can use the send method for private and protected methods if you wanna use them without visibility limitations;
- usually, the send method is very useful when testing private and protected methods, otherwise, it is rather unnecessary than necessary;
- maybe instead of the send method, you need  the public_send which respects the visibility of the method and doesn't break the encapsulation;
- the visibility of the method can be redefined the same way as the methods themselves;
- with private_instance_methods(false), protected_instance_methods(false), public_instance_methods(false) you can list all methods by their visibility;
- dinosaurs are cute creatures :)


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/common-notes-about-public-private-and-protected-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/common-notes-about-public-private-and-protected-methods-in-ruby</a>
