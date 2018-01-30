##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/immutable-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/immutable-methods-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Immutable methods in Ruby

In this article, we'll look at what imitable methods are and how they work.

----------
This article is divided into the following sections:

 1. [What are immutable methods in ruby](#)
 2. [The logic of immutable methods](#)
 3. [Summary](#summary)
----------
## What are immutable methods in ruby
Methods that return a copy of the object and don't change its state are called immutalbe methods, or safe methods. Typically, this is the majority of methods that you will see in ruby.

For example, String class has the swapcase method. Let's see how it works:

	a = "string"
	puts "Object id of 'a': #{a.object_id}"
	puts a.swapcase
	puts a
	puts "Object id of 'a': #{a.object_id}"
produces:

	Object id of 'a': 47082269429180
	STRING
	string
	Object id of 'a': 47082269429180
	=> nil   

As we can see, after we called the swapcase method, we got the result in the form of a string that was written in upper case. However, the object itself wasn't changed. In the end, we have the same object and the same line written in lower case.


----------
## The logic of immutable methods
Let's look at how immutable methods work in more detail:

1) inside the method we create a copy of the received object;
2) then we make changes on the copied object;
3) then we return the modified copy of the object

So, the original object stays unchanged. Let's use knowledge in practice and create an "original" safe method. To copy the object we will use the load and dump methods that are defined in the Marshal module because the standard ruby library doesn't have native methods for deep copy.

Our code:

	class TestObject

	  attr_accessor :name

	  def deep_clone(object)
	    Marshal.load(Marshal.dump(object))
	  end

	  def modify_object
	    copy = deep_clone(self)
	    copy.name.swapcase!
	    copy
	  end

	end

In this example we see 2 methods. The deep_clone method will create a copy of the object that will have different object id. Then we will change the copy using swapcase! and return the modified object.

	test = TestObject.new
	test.name = "Test name"
	puts test.modify_object
	puts test.name

produces:

	tEST NAME
	Test name
	=> nil   


## Summary
----------
- methods that don't change the state of an object are called immutable methods
- most methods in ruby are immutable methods

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/immutable-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/immutable-methods-in-ruby</a>
