##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameter-with-double-splat-operator-in-ruby" target="_blank">https://www.alexindev.com/posts/parameter-with-double-splat-operator-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Parameter with double splat operator (**) in Ruby

In this article, we will consider the features of working with a parameter with double splat operator (**).

----------
This article is divided into the following sections:

 1. [The syntax of parameter with double splat operator](#)
 2. [Created specifically for processing hashes](#)
 3. [Parameter with double splat operator is optional](#)
 4. [Works only with one hash](#)
 5. [Must be the last parameter in a parameter list](#)
 6. [The rule of the last hash parameter is also valid for a parameter with the double splat operator](#)
 7. [Keyword parameter plus parameter with double splat operator are compatible](#)
 8. [Conclusions](#)
----------
## The syntax of parameter with double splat operator
A parameter with double splat operator is a parameter before which we use two splat operators (**).
Here's how it looks:

	def method_name(**double_splat)
	  #method body
	end


----------
## Created specifically for processing hashes
A parameter with double splat operator only works if we pass a hash to it. When we use objects of other classes, we get an error.

Let's define a method for our example. We will name it 'print_list_of' and define its parameter 'books_and_artilces' with the double splat operator added in front of it - '** books_and_articles'. The method will output the names of our favorite books and our favorite articles from these books:

	def print_list_of(**books_and_articles)
	  books_and_articles.each do |book, article|
	    puts book
	    puts article
	  end
	end

	# As an argument, we define a hash in which we will write books and articles.
	books_and_articles_we_love = {
	  "Ruby on Rails 4": "What is webpack?",
	  "Ruby essentials": "What is Ruby Object Model?",
	  "Javascript essentials": "What is Object?"
	}

	print_list_of(books_and_articles_we_love)

let's run the code:

	Ruby on Rails 4
	What is webpack?
	Ruby essentials
	What is Ruby Object Model?
	Javascript essentials
	What is Object?
	=> {:"Ruby on Rails 4"=>"What is webpack?", :"Ruby essentials"=>"What is Ruby Object Model?", :"Javascript essentials"=>"What is Object?"}  

Now let's try to pass an object of another class, for example, an instance of the String class:

	def test_method(**parameter)
	  puts books_and_articles
	end

	test_method("aaa")
produces:

	wrong number of arguments (given 1, expected 0)
	(repl):1:in `test_method'
	(repl):5:in `<main>'   

Thus, a parameter with the double splat operator works only with hashes.


----------
## Parameter with double splat operator is optional
Let's rewrite one of the previous examples and use the usual parameter instead of the parameter with double splat operator:

	def print_list_of(books_and_articles)
	  books_and_articles.each do |book, article|
	    puts book
	    puts article
	  end
	end


	books_and_articles_we_love = {
	  "Ruby on Rails 4": "What is webpack?",
	  "Ruby essentials": "What is Ruby Object Model?",
	  "Javascript essentials": "What is Object?"
	}

	print_list_of(books_and_articles_we_love)

Let's run it:

	Ruby on Rails 4
	What is webpack?
	Ruby essentials
	What is Ruby Object Model?
	Javascript essentials
	What is Object?
	# => {:"Ruby on Rails 4"=>"What is webpack?", :"Ruby essentials"=>"What is Ruby Object Model?", :"Javascript essentials"=>"What is Object?"}  

As we can see, we can pass a hash without using a parameter with the double splat operator.

What's the difference? Why should we use a parameter with the double splat operator if we can get the same result without it? The uniqueness of using a parameter with the double splat operator is that it's optional. In other words, if we do not pass a hash, then there will be no error, and a local variable inside a method will refer to an empty hash ({}).

For instance:

	def test_method(**parameter)
	  puts parameter
	end

	test_method

produces:

	{}
	=> nil

Thus, if you need to have a hash passed, then it's better to use a positional parameter, but if your code assumes that in some cases when the method is called, a hash will not be passed, then you can use a parameter with double splat operator.


----------
## Works only with one hash
Since we already saw how a parameter with the splat operator works, we can assume that if we use a parameter with the double splat operator, then the effect from use will likely be similar.

Let's check this:

	def print_book_and_article(**hash_and_hash)
	  puts hash_and_hash.inspect
	end

	books_and_articles_we_love = {
	  "Ruby on Rails 4": "What is webpack?",
	  "Ruby essentials": "What is Ruby Object Model?",
	  "Javascript essentials": "What is Object?"
	}

	second_list_of_books_and_articles_we_love = {
	  "Ruby on Rails 4": "What is ActiveRecord?",
	  "Ruby essentials": "What is String class?",
	  "Javascript essentials": "Propertes of objects"
	}

	print_book_and_article(books_and_articles_we_love, second_list_of_books_and_articles_we_love)

now let's run our example:

	wrong number of arguments (given 1, expected 0)
	(repl):1:in `print_book_and_article'
	(repl):17:in `<main>'   

As you can see, the parameter with the double splat operator is quite different from the parameter with one splat operator. If we pass one hash then this code will be executed without errors:

	print_book_and_article(books_and_articles_we_love)
produces:

	{:"Ruby on Rails 4"=>"What is webpack?", :"Ruby essentials"=>"What is Ruby Object Model?", :"Javascript essentials"=>"What is Object?"}
	=> nil   


----------
## Must be the last parameter in a parameter list
If a method has several parameters and we want to use a parameter with the double splat operator, then it should be placed last in a list of parameters.

For instance:


	def list(first_books_and_articles, **second_books_and_articles)
	  first_books_and_articles.each { |key, value| puts key; puts value }
	  second_books_and_articles.each { |key, value| puts key; puts value }
	end

	# it will be our first argument
	books_and_articles_we_love = [
	  "Ruby on Rails 4": "What is webpack?",
	  "Ruby essentials": "What is Ruby Object Model?",
	  "Javascript essentials": "What is Object?"
	]

	# out second argument
	second_list_of_books_and_articles_we_love = {
	  "Ruby on Rails 4": "What is ActiveRecord?",
	  "Ruby essentials": "What is String class?",
	  "Javascript essentials": "Propertes of objects"
	}

	list(books_and_articles_we_love, second_list_of_books_and_articles_we_love)

let's run it:

	{:"Ruby on Rails 4"=>"What is webpack?", :"Ruby essentials"=>"What is Ruby Object Model?", :"Javascript essentials"=>"What is Object?"}

	Ruby on Rails 4
	What is ActiveRecord?
	Ruby essentials
	What is String class?
	Javascript essentials
	Propertes of objects
	=> {:"Ruby on Rails 4"=>"What is ActiveRecord?", :"Ruby essentials"=>"What is String class?", :"Javascript essentials"=>"Propertes of objects"}   

Now, if we swap parameters and arguments, we will see an error:

	def list(**second_books_and_articles, first_books_and_articles)
	  ## same code
	end

	list(second_list_of_books_and_articles_we_love, books_and_articles_we_love)

produces:

	wrong number of arguments (given 2, expected 1)
	(repl):1:in `list'
	(repl):20:in `<main>'   


----------
## The rule of the last hash parameter is also valid for a parameter with the double splat operator
In ruby, the last parameter works like a hash parameter. In other words, if we pass a group of hashes as an argument, then the last parameter will take all hashes as one object.


	def test_method(a,b)
	  puts a.inspect
	  puts b.inspect
	end

	test_method("Phrase one", first_hash: "Phrase two", second_hash: "Phrase three", third_hash: "Phrase four")
produces:


	"Phrase one"
	{:first_hash=>"Phrase two", :sec>"Phrase three", :third_hash=>"Phrase four"}
	=> nil

Also, the same rule is valid for a parameter with the double splat operator:

	def test_method(a,**b)
	  puts a.inspect
	  puts b.inspect
	end

	test_method("Phrase one", first_hash: "Phrase two", second_hash: "Phrase three", third_hash: "Phrase four")

produces:

	"Phrase one"
	{:first_hash=>"Phrase two", :sec>"Phrase three", :third_hash=>"Phrase four"}
	=> nil   


----------
## keyword parameter plus parameter with double splat operator are compatible
As we found in the previous example, if we define hashes as arguments, then they are all assigned to a local variable that has a name of the parameter that was defined using the double splat operator.

What will happen if we use a keyword parameter? Let's define a method in which there will be 2 parameters, one will be a keyword parameter, and the second one will be determined with the help of the double splat operator.

Our method:

	def print_pheases(phrase:, **article_and_phrase)
	  puts phrase
	  puts article_and_phrase
	end

	print_pheases(phrase: "some phrase", phrase_two: "some phrase two", phrase_three: "some phrase three")
Let's run it:

	some phrase
	{:phrase_two=>"some phrase two", :phrase_three=>"some phrase three"}
	=> nil   


As we see, ruby decided that the first argument refers to the keyword parameter. This behavior is fairly predictable, therefore using these parameters together we will not mislead anyone.


----------
## Conclusions
- in ruby, we can define a parameter with the double splat operator (**)
- this type of parameter created specifically for processing hashes
- parameter with double splat operator is optional
- works only with one hash
- must be the last parameter in a parameter list, in another case we will see an error
- the rule of the last hash parameter is also valid for a parameter with the double splat operator
- keyword parameter plus parameter with double splat operator are compatible

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/parameter-with-double-splat-operator-in-ruby" target="_blank">https://www.alexindev.com/posts/parameter-with-double-splat-operator-in-ruby</a>
