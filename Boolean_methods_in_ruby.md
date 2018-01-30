##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/boolean-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/boolean-methods-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Boolean methods in ruby

In this article, we'll look at what boolean methods are and how they work.

----------
This article is divided into the following sections:

 1. [What are Boolean methods in ruby ](#what-are-boolean-methods-in-ruby)
 2. [The question mark is used when the method returns boolean value](#the-question-mark-is-used-when-the-method-returns-boolean-value)
 3. [Perfectly combined with comparison operators](#perfectly-combined-with-comparison-operators)
 4. [Conclusions](#conclusions)
----------
## What are Boolean methods in ruby
Methods that end with the question mark (?) in ruby has a few names: predicates, query or boolean methods. It's assumed that by these methods we ask a certain question and can get certain information about the object. For example, ruby has the method 'any?'. With it, we can ask an array if it has any elements inside it and as a result, we will get 'true' or 'false'.

	puts [].any?
	puts ["element one", "element two", "element three"].any?

produces:

	false
	true
	=> nil
More examples:

	irb(main):078:0* "daadsasd".is_a?(String)
	=> true
	irb(main):079:0> "daadsasd".tainted?
	=> false
	irb(main):080:0> "daadsasd".nil?
	=> false
	irb(main):081:0> "daadsasd".respond_to?("downcase!")
	=> true


----------
## The question mark is used when the method returns boolean value
From the previous examples, it's obvious that the question mark (?) at the end of the method is set when we have a method that returns boolean value - 'true' or 'false'.


----------
## Perfectly combined with comparison operators
Since comparison methods typically return either true or false, they are perfectly matched to the concept of Boolean methods.

For instance:

	def even?(x)
	  (x % 2) == 0
	end

	def more_then_five?(x)
	  x > 5
	end

	def less_then_three?(x)
	  x < 3
	end

	puts even?(2)
	puts even?(3)

	puts more_then_five?(3)
	puts more_then_five?(6)

	puts less_then_three?(2)
	puts less_then_three?(4)

produces:

	true
	false
	false
	true
	true
	false
	=> nil  


----------
## Conclusions
  - Methods that end with the question mark (?) are called boolean methods;
  - Boolean methods must return true or false;
  - Boolean methods are ideally combined with comparison operators.


----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/boolean-methods-in-ruby" target="_blank">https://www.alexindev.com/posts/boolean-methods-in-ruby</a>
