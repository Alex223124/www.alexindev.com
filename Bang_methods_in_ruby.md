##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/bang-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/bang-methods-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Bang methods in ruby

In this article, we'll look at what boolean methods are and how they work.

----------
This article is divided into the following sections:

 1. [What are bang methods in ruby](#what-are-bang-methods-in-ruby)
 2. [Bang method must return a modified object](#bang-method-must-return-a-modified-object)
 3. [Bang methods can return nil](#bang-methods-can-return-nil)
 4. [Usually, bang method has its non-bang equivalent](#usually-bang-method-has-its-non-bang-equivalent)
 5. [Summary](#summary)
----------
## What are bang methods in ruby
Methods that end with the exclamation mark, for example, downcase! or compact! are called bang methods ( also mutator methods, dangerous methods). The usual methods perform an action on a copy of the object and then return copy, bang methods - perform an action and change the object, returning the modified object as a result. Sometimes this is called a 'mutation' of the object. The exclamation mark, in this case, is usually associated with an attempt to send the message: "Warning! This method can be dangerous! It will change the value of the object!"


----------
## Bang method must return a modified object
If we put the exclamation mark at the end of the method, our method, of course, will not work as a bang method. In order for our method to conform to the convention, it must return the same object that was changed. You can easily achieve this in ruby, it's enough to use another bang method for this.

For instance:


	class ExampleClass

	  attr_accessor :number

	  def remove_whitespace!
	    @number.strip!
	  end

	end

	a = ExampleClass.new
	a.number = "    green"
	puts a.number
	puts "----------------"
	puts a.remove_whitespace!
	puts "----------------"
	puts a.number

produces:


	    green
	----------------
	green
	----------------
	green
	=> nil   

As we can see, the value of the instance variable @number has changed. However, there is a small problem here. Although we see that the value has changed, this may not be enough to say is this the same object or not. Quite often it's visually impossible to understand which object is in front of you, especially if we have a fairly large method, and then the 'object_id' method comes to the rescue.

This method is defined in the Object class that makes it available to all ruby objects. The job of this method is to tell us which object id has the object.

For instance, we have the following code:

	puts "---------------"
	a = "123"
	puts a.object_id
	puts "---------------"
	a = "123"
	puts a.object_id
	puts "---------------"

produces:

	---------------
	47353786377940
	---------------
	47353789471600
	---------------
	=> nil   

As you can see, we have 2 visually identical objects, but they have different object ids, which tells us that these objects are different and that they are stored in different places.

Now, let's try another example. In the new example, we will extend the String class and add the change_color! method which will use the gsub! method for changing object's value.

	class String

	  def change_color!
	    self.gsub!(/yellow/, "red")
	  end

	end

	a = "yellow submarine"
	puts "Initial value of a: #{a}"
	puts "Object id of 'a': #{a.object_id}"
	a.change_color!
	puts "Object id of 'a': #{a.object_id}"
	puts "Value of 'a': #{a}"

produces:

	Initial value of a: yellow submarine
	Object id of 'a': 47290541645240
	Object id of 'a': 47290541645240
	Value of 'a': red submarine
	=> nil

As you can see, we changed the value of the object and it has the same object id in both cases.


----------
## Bang methods can return nil
A lot of bang methods have a distinctive feature. If they don't change the object, they return nil.

For instance:

	puts 'cat'.upcase!
	puts 'CAT'.upcase!
produces:


	CAT

	=> nil   
As you can see, in the second case we got nil as the returned value. This is because in the string 'CAT' all the characters already were written in the upper case and the method upcase! doesn't know what to do. That's why there are can be situations when in a conditional expression you get nil and the condition will be work in an unexpected way.

For instance:

	cat_name = "CAT"
	formatted_cat_name = cat_name.upcase!

	if formatted_cat_name
	  puts "Hi #{formatted_cat_name}! Kitty! Kitty! Kitty!"
	else
	  puts "Kitty! Kitty! Kitty!"
	end

produces:

	Kitty! Kitty! Kitty!
	=> nil   

As you can see, despite the fact that we have a cat's name, we can't normally say hello :) Thus, if you need to get exactly true or false then you should think about scenarios when you will get nil instead of something that will be evaluated as true.


----------
## Usually, bang method has its non-bang equivalent
For example, the following methods are defined in the String class:

	:upcase, :upcase!, :downcase, :downcase!, :capitalize, :capitalize!, :swapcase, :swapcase!
Let's use those methods:

	a = "first string"
	a.upcase
	puts "Object id of 'a': #{a.object_id}"
	a.upcase!
	puts "Object id of 'a': #{a.object_id}"
	puts a

	b = "SECOND STRING"
	b.downcase
	puts "Object id of 'b': #{b.object_id}"
	b.downcase!
	puts "Object id of 'b': #{b.object_id}"
	puts b

	c = "third string"
	c.capitalize
	puts "Object id of 'c': #{c.object_id}"
	c.capitalize!
	puts "Object id of 'c': #{c.object_id}"
	puts c

produces:

	Object id of 'a': 47254336344720
	Object id of 'a': 47254336344720
	FIRST STRING
	Object id of 'b': 47254336341460
	Object id of 'b': 47254336341460
	second string
	Object id of 'c': 47254336315140
	Object id of 'c': 47254336315140
	Third string
	=> nil  


----------
## Summary
- according to the convention if your method changes the object value (mutates object) then it must end with the exclamation mark;
- bang methods can return nil;
- you can determine whether the method is a beng method using the object_id method;
- a lot of bang methods has its non-bang equivalents.

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/bang-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/bang-methods-in-ruby</a>
