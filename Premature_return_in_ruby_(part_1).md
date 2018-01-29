##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/premature-return-in-ruby-part-1-2" target="_blank">https://www.alexindev.com/posts/premature-return-in-ruby-part-1-2</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
# Premature return in ruby (part 1/2)

In this article, we will consider the features of the premature return and in what situations you can use it to make your code more effective.

----------
This article is divided into the following sections:

 1. [What is a premature return?](#what-is-a-premature-return)
 2. [The return keyword works only once](#the-return-keyword-works-only-once)
 3. [It's not recommended to place the return keyword at the center of the method](#its-not-recommended-to-place-the-return-keyword-at-the-center-of-the-method)
 4. [The return keyword can hide an error in the code](#the-return-keyword-can-hide-an-error-in-the-code)
 5. [Don't use the return keyword for the last line of a method - in ruby it's not necessary
](#dont-use-the-return-keyword-for-the-last-line-of-a-method---in-ruby-its-not-necessary)
 6. [Conclusions](#conclusions)


----------
## What is a premature return?
Quite often there are situations when it's necessary to interrupt the execution of the method and return some specific result.

In this case, we can use the return keyword, which will help us to abort the execution of a method and output the result of some particular line of code from a method.

Syntax:

	def method_name
	  return statement if condition
	  statement
	end

Let's see an example. We have a method in which there are 2 lines of code:

	def sum(a,b)
	  (a + b) if a > 2
	  b + b
	end

	puts "first method call"
	puts sum(1,2)
	puts "second method call"
	puts sum(3,4)

In this example, if the value of the local variable is greater than 2, the method will calculate the first line, then it will calculate the second one and return the result as the return value.

Let's run our example:

	first method call
	4
	second method call
	8
	=> nil   

But what if we want to get the result of the first line as the return value? Of course, we can rewrite the method and use the if..else condition:

	def sum(a,b)
	  if a > 2
	    a + b
	  else
	    b + b
	  end
	end

	puts "first method call"
	puts sum(1,2)
	puts "second method call"
	puts sum(3,4)

produces:

	first method call
	4
	second method call
	7
	=> nil

However, in practice, we are not always able to apply this approach. And in some cases, the use of if..else and similar conditions can lead to entanglement of a method (we will talk about it at the end of this article). Another tool that allows us to more flexibly control a method is the return keyword.

Let's use the method from the first example and add it to the first line:

	def sum(a,b)
	  return (a + b) if a > 2
	  b + b
	end

Now, if the 'if' condition is satisfied, the method will be aborted and ruby will return the result of the expression '(a + b)'. The second line will not be evaluated by the interpreter.

Let's use our method:

	puts "first method call"
	puts sum(1,2)
	puts "second method call"
	puts sum(3,4)

produces:

	first method call
	4
	second method call
	7
	=> nil

Thus, in certain situations, when we need flexibility, we can explicitly indicate the result of what line we want to receive as a return value using the return keyword.


----------
## The return keyword works only once
The keyword return should be used only once. In other words, if it will be called once the second one will not be called.

For instance:

	def method_with_two_retruns
	  return "First string"
	  return "Second string"
	end

	method_with_two_retruns
produces:

	=> "First string"  
There is no sense in this code because the interpreter will not reach the second line. But there are situations when we can (and even should) use several return statements, but still, only one of them will be called.

Example:

	def print_some_object(first_object, second_object, third_object)
	  return first_object if first_object.is_a?(String)
	  return second_object if second_object.is_a?(Array)
	  return third_object if third_object.is_a?(Hash)
	end

Description of the example:

 1. We defined the print_some_object method which has 3 position
    parameters first_object, second_object, third_object;
 2. Inside the code, we use the return keyword 3 times. One of them will be executed if the code after the 'if' condition will return true;
 3. For the condition, we use an object that we get through the arguments (first_object) and by using the is_a?(arg) method, we check whether an object belongs to a certain class, as a result, the call of this method should return true or false;

First, let's call the method and pass 3 arrays as the argument:

	print_some_object(["This","is","first"], ["This","is","second"], ["This","is","third"])
produces:

	=> ["This", "is", "second"]  
As you can see, the method returned only the second object, because only it came under the condition.

Now, let's run the method with other arguments, as arguments we will use 3 objects of different classes, the string - "This is string", the array - ["This", "is", "Array"] and the hash {hash_key: "This is hash "}.

Code:

	print_some_object("This is string", ["This","is","Array"], {hash_key: "This is hash"})

produces:

	=> "This is string"  

As you can see, the interpreter executes only the first line and returns the string to us. The rest of the lines are ignored since as soon as the interpreter encounters the return keyword, it performs the return from the method. For the integrity of the picture, we can see one more example. Let's write a method in which all 'return' keywords are replaced by a call of the puts method.

	def print_some_object(first_object, second_object, third_object)
	  puts first_object if first_object.is_a?(String)
	  puts second_object if second_object.is_a?(Array)
	  puts third_object if third_object.is_a?(Hash)
	end

	print_some_object("This is string", ["This","is","Array"], {hash_key: "This is hash"})

produces:

	This is string
	This
	is
	Array
	{:hash_key=>"This is hash"}
	=> nil  

As you can see, the method displayed the values of the objects using the puts method and returned nil as the return value.

In other words, all 3 arguments are fit for conditional expressions, but since we didn't use explicit return using the return keyword, the method executed all expressions and returned the result of the last line as the return value.

----------
## It's not recommended to place the return keyword at the center of the method

If we use the return keyword, then we shouldn't do this somewhere in the center of the method. We should always evaluate what we wrote. For instance, in the example below, the interpreter will never get to the last line of the method and such code will mislead other developers.

Example:

	def say_something_unusual(something)
	  phrase = "Wow!!" + something + "!!!!"
	  return something
	  puts phrase
	end

	say_something_unusual("Hi! I'm dinosaur")

produces:

	=> "Hi! I'm dinosaur"  

Moreover, the watchful reader could notice that if the interpreter doesn't get to the last line of the method, then the first line of the method loses all meaning. Because the local variable 'phrase' will not be used by the method. If our task is to return the phrase that we pass to the method, then we must remove all unnecessary code:

	def say_something_unusual(something)
	  return something
	end

	say_something_unusual("Hi! I'm dinosaur")

produces:

	=> "Hi! I'm dinosaur"   

Now our method is more readable. However, since the method consists of one line of code, the use of the return keyword is also redundant:

	def say_something_unusual(something)
	  something
	end

	say_something_unusual("Hi! I'm dinosaur")

produces:

	=> "Hi! I'm dinosaur"


----------
## The return keyword can hide an error in the code

Since ruby is not a compiled language, we can write code that, when executed, will interrupt the execution of the program and read about it only during the execution of the program. Sometimes in practice, there are cases when due to conditions a certain code is never executed. And so we can get the situation when there is an error in the code, but we don't see it.

This feature is useful for knowing when debugging because there are situations when most of the time a method uses the return keyword and at a certain iteration the program stops its execution because it executes code that has not been supported for a long time.

For instance:

	def some_method_with_mistake(arg)
	  return "Something usual" if arg > 1
	  "String" + 1
	end

Let's suppose that in 99% of cases the method is called with an argument that is greater than one:

	some_method_with_mistake(2)
	some_method_with_mistake(3)
	some_method_with_mistake(4)

produces:

	=> "Something usual"   

Now, suddenly we got an argument that is equal to one:

	some_method_with_mistake(1)

produces:

	no implicit conversion of Fixnum into String
	(repl):3:in `+'
	(repl):3:in `some_method_with_mistake'
	(repl):6:in `<main>'   

Thus, as in the case of working with other kinds of conditions in ruby, it's necessary to check in advance whether all the code we wrote is valid.


----------
## Don't use the return keyword for the last line of a method - in ruby it's not necessary
The programmers who switched from JavaScript quite often use the return keyword in all methods. This is due to the fact that in JS if we don't specify return then a method will decide that we don't want to return anything. In ruby we have opposite situation, there is no difference whether we use the return keyword in the last line of a method or not. By default, the result of the last line of a method is the return value.

Example:

	def print_word(word)
	  puts "Hello from method!"
	  word
	end

	def print_word_variant_two(word)
	  puts "Hello from method two!"
	  return word
	end

	a = print_word("Earth")
	puts a
	b = print_word_variant_two("Earth")
	puts b

produces:

	Hello from method!
	Earth
	Hello from method two!
	Earth
	=> nil

Thus, use the return keyword if it really has a semantic load, it is not accepted in ruby to explicitly specify the return value for each method.


----------
## Conclusions
- in certain situations, when we need flexibility, we can explicitly indicate the result of what line we want to receive as a return value using the return keyword;
- the return keyword works only once;
- it's not recommended to place the return keyword at the center of the method;
- the return keyword can hide an error in the code;
- don't use the return keyword for the last line of a method - in ruby it's not necessary.

----------

##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/premature-return-in-ruby-part-1-2" target="_blank">https://www.alexindev.com/posts/premature-return-in-ruby-part-1-2</a>
