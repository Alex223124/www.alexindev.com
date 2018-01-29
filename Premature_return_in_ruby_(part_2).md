# Premature return in ruby (part 2/2)

In this article, we will continue to consider the features of the premature return from a method and when we should use it.

----------
This article is divided into the following sections:

 1. [Empty return returns nil](#Empty-return-returns-nil)
 2. [We can implicitly define an array](#We-can-implicitly-define-an-array)
 3. [The return keyword is used when you need to exit a method before the last line in a method is executed](#The-return-keyword-is-used-when-you-need-to-exit-a-method-before-the-last-line-in-a-method-is-executed)
 4. [The return keyword is used to interrupt the call stack](#The-return-keyword-is-used-to-interrupt-the-call-stack)
 5. [The return keyword is useful for refactoring massive validations](#The-return-keyword-is-useful-for-refactoring-massive-validations)
 6. [The return keyword and the error "stack level too deep"](#The-return-keyword-and-the-error-"stack-level-too-deep")
 7. [Conclusions](#Conclusions)



----------
## Empty return returns nil
If we don't define any object (or expression) to which the keyword return refers, then a method returns nil.

Example:

    def method_name
	  return
	end

	method_name

produces:

    => nil   



----------
## We can implicitly define an array
As we know, we can explicitly move several objects inside an array if we need to return many objects. With the return keyword we can do the same thing.

Example:

    def some_method
      ["first object", "second object", "third object"]
    end

    some_method

produces:

    => ["first object", "second object", "third object"]   

There is another way to achieve a similar result. We can list the objects that we want to return as an array and use the return keyword, separated by commas.

Let's see an example:

    def some_method
      return "Object one", "Object two", "Object three"
    end

	a = some_method
	puts a.inspect

produces:

    ["Object one", "Object two", "Object three"]
    => nil  

So, this is another way to return an array.


----------
## The return keyword is used when you need to exit a method before the last line in a method is executed

For instance, this is very useful in combination with conditional operators:

    def retrun_something(arg)
      if arg > 1
        "If case"
      else
        return "Else case"
	  end
	  "Something from outside of if-esle-end"
	end

	puts "First flow:"
	a = retrun_something(2)
	puts a

	puts "Second flow:"
	a = retrun_something(0)
	puts a

 produces:

	First flow:
	Something from outside of if-esle-end
	Second flow:
	Else case
	=> nil  

Description of the code:

The first method call:

 1. In the first case, we passed 2 as the argument;
 2. Since 2 is greater than 1 then the if condition is satisfied;
 3. Since before "If case" we don't have the return keyword, the interpreter goes on and executes the string "Something from outside of if-esle-end";
 4. Then the string "Something from outside of if-else-end" is assigned to the variable 'a' because this is the result of the last line of the method;
 5. Then we print the value of the variable 'a' with the puts method.

The second method call:

 1. We passed 0 as the argument;
 2. Since 0 is less than 1 then the code that goes after the 'else' part of the condition is executed;
 3. The method returns the result of what is written after the return keyword, the string "Else case";
 4. Then we assign this value to the variable 'a' and output it using the puts method.

Why is it useful? In this example, if we remove the return keyword, then in one of the variants of the method execution (else case) the return value is the output of the string "Something outsite if-esle-end". But we need to output exactly what goes in the else case! Here, the return keyword comes to the rescue.

"But the method can be written differently, and you don't need to use  the return keyword." - An experienced programmer can say. In applications, there can be complex methods (for example on 300 lines), when we can't simply take and rewrite everything, and then this possibility is very useful. Of course, you don't need to strive for such large methods, but it is better to add the return keyword than to spend a month on rewriting such code without any guarantee that this will bring some benefit.


----------
## The return keyword is used to interrupt the call stack

This approach is common in Ruby on Rails applications. In order to abort the execution of the code of a certain request-response cycle, we add a condition under which the whole cycle is interrupted. The benefit can be explained by the fact that the user doesn't receive a response with an invalid data, or the data will not be saved to a database that didn't pass a corresponding check.

The general meaning can be illustrated by this example:

	class ExampleClass

	  def method_one(parameter)
	    puts "Lets call method_two!"
	    method_two(parameter)
	  end

	  def method_two(parameter)
	    puts "Lets call method_three!"
	    method_three(parameter)
	  end

	  def method_three(parameter)
	    puts "Lets call method_four!"
	    method_four(parameter)
	  end

	  def method_four(parameter)
	    return false if parameter > 5
	    puts "Lets call method five!"
	    method_five(parameter)
	  end

	  def method_five(parameter)
	    puts "Lets save data!"
	    @data = parameter
	    puts "Current state of @data: #{@data}"
	  end

	end

Description of the example:

In the example, several methods are defined, each of which calls the next one. Let's suppose that in a certain method we have received a wrong object and we need to urgently interrupt the execution of the following methods, otherwise damage will be done to a data of the application. The value of the variable @data will symbolize the database of our application, where there shouldn't be a value that satisfies the expression: 'parameter > 5'. Therefore, we will try to prevent this.

First use of the class:

	puts "Method call with invalid data"
	new_instance = ExampleClass.new.method_one(6)

produces:

	Method call with invalid data
	Lets call method_two!
	Lets call method_three!
	Lets call method_four!
	=> false  

As you can see, the interpreter was interrupted in the method_four method. Because the variable @data wasn't declared during the execution of the program means that there are no incorrect data there.

The second use of the class:

	new_instance = ExampleClass.new.method_one(2)

produces:

	Lets call method_two!
	Lets call method_three!
	Lets call method_four!
	Lets call method five!
	Lets save data!
	Current state of @data: 2
	=> nil  

Now the interpreter has gone through all the methods and we have the value @data which is 2. Thus, we can use the return keyword in connection with false to interrupt the call stack if some undesirable things happen in a program.


----------
## The return keyword is useful for refactoring massive validations
In ruby, a replacement of massive checks in a method using the return keyword is a common pattern. For instance, we have a method that checks the current object for many conditions:

	def check_something
	  if condition_one? || condition_two? || condition_three? || condition_four?
	    "something wrong "
	  elsif condition_five? && condition_six? || condition_seven? && condition_eight?
	    "something different, but still wrong"
	  elsif condition_two? && condition_five? || condition_one? && ondition_eight?
	    "something mooore different, but unfortunately wrong"
	  elsif condition_three? && condition_one? || condition_ten?
	    "The computer will explode in 5 seconds, run run run"
	  else
	    true
	  end
	end

It doesn't look very expressive. Moreover, if you decide to use binding.pry to dig the situation deeper, you will have to check the group of conditions one by one, then check the conditions together to understand why you get one object instead of the other as the return value.

Let's rewrite it using the return keyword:

	def check_something
	  return "something wrong" if condition_one?
	  return "something wrong" if condition_two?
	  return "something wrong" if condition_three?
	  return "something wrong" if condition_four?

	  return "something different, but still wrong" if (condition_five? && condition_six?)
	  return "something different, but still wrong" if (condition_seven? && condition_eight?)

	  return "something mooore different, but unfortunately wrong" if (condition_two? && condition_five?)
	  return "something mooore different, but unfortunately wrong" if (condition_one? && ondition_eight?)

	  return "The computer will explode in 5 seconds, run run run" if (condition_three? && condition_one?)
	  return "The computer will explode in 5 seconds, run run run" if condition_ten?

	  return true
	end

In the case of the return keyword, you can step through each of the individual conditions. Moreover, you know how many variants of return from the method exist and you can put debugger after each expression. For additional readability, we have empty lines inside the method. Think about which code you prefer? I definitely like the last one.


----------
## The return keyword and the error "stack level too deep"
Another basic thing to consider when working with the explicit return is that we can lock the chain of calls and thereby get a SystemStackError error.

Example:

	def a
	  return b
	end

	def b
	  return c
	end

	def c
	  return a
	end

	a

produces:

	stack level too deep
	(repl):2:in `a'
	(repl):10:in `c'
	(repl):6:in `b'
	(repl):2:in `a'
	(repl):10:in `c'
	(repl):6:in `b'
	(repl):2:in `a'
	(repl):10:in `c'
	(repl):6:in `b'
	(repl):2:in `a'
	(repl):10:in `c'
	(repl):6:in `b'
	(repl):2:in `a'
	(repl):10:in `c'

As we can see, each time we return from the method, we call another method which has a call to the other. Thus, the methods call each other and such program could work forever, but ruby standard library has the error for this case.


----------
## Conclusions

- empty return returns nil;
- we can implicitly define an array;
- the return keyword is used when you need to exit a method before the last line in a method is executed;
- the return keyword is used to interrupt the call stack;
- the return keyword is useful for refactoring massive validations;
- the return keyword and the error "stack level too deep".
