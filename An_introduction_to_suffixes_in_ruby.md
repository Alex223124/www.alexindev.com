##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-suffixes-in-ruby" target="_blank">https://www.alexindev.com/posts/an-introduction-to-suffixes-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## An introduction to suffixes in ruby

In this article, we will consider the introductory theses on how suffixes work in ruby methods.

----------
This article is divided into the following sections:

 1. [Ruby use suffixes in method names for greater code readability](#)
 2. [The rules about what suffixes and when to use are called conventions](#)
 3. [Suffixes are only part of the method name and nothing else](#)
 4. [Ruby has a limited number of suffixes](#)
 5. [We can work around the rules of creating suffixes using the define_method method and the send method](#)
 6. [Summary](#)
----------
## Ruby use suffixes in method names for greater code readability
An attentive programmer may notice that in ruby there are methods with suffixes.

For instance:

	puts [].any?
	puts ['a','b'].map!{|x| x+='c'}
	puts "AAA".downcase!
	puts ["hey", "you"].nil?
	puts [ ["a"], ["b"] ].flatten!

produces:

	false
	ac
	bc
	aaa
	false
	a
	b
	=> nil   

As we see, we have several expressions in which some methods end with the question mark (?) and the exclamation mark (!).
A common example of using equal (=) suffix is a setter method:

	class Entity

	  #getter
	  def coordinates
	    @coordinates
	  end

	  # setter
	  def coordinates=(new_coordinates)
	    @coordinates = new_coordinates
	  end

	end

The question is - why some methods end with those suffixes? And in which case mathod should end with one or another suffix?

Let's look at example:

	class Bookshelf

	  CAPACITY = 10

	  def amount
	    "The number of books on the shelf: #{@books}"
	  end   

	  #getter
	  def books
	    @books
	  end

	  # setter
	  def books=(books)
	    @books = books
	  end

	  def rearrange!
	    @books.reverse!
	  end

	  def is_full?
	    @books.length == CAPACITY
	  end

	end
In this example, we have class Bookshelf. In the class, we have several methods for work with a bookshelf. We can check whether it's filled with the 'is_full?' method or we can reorganize a layout of books with the 'rearrange!' method.

Let's create a new bookshelf and play a little with it:

	bookshlef = Bookshelf.new
	bookshlef.books = ["book 1", "book 2", "book 3", "book 4", "book 5", "book 6"]
	puts bookshlef.books
	puts bookshlef.amount
	puts "-------------"
	bookshlef.rearrange!
	puts "-------------"
	puts bookshlef.books
	puts "-------------"
	puts bookshlef.is_full?
	puts "-------------"
	bookshlef.books = ["book 1", "book 2", "book 3", "book 4", "book 5", "book 6", "book 7", "book 8", "book 9", "book 10"]
	puts "-------------"
	puts bookshlef.is_full?
	puts bookshlef.amount

produces:


	book 1
	book 2
	book 3
	book 4
	book 5
	book 6
	The number of books on the shelf: ["book 1", "book 2", "book 3", "book 4", "book 5", "book 6"]
	-------------
	-------------
	book 6
	book 5
	book 4
	book 3
	book 2
	book 1
	-------------
	false
	-------------
	-------------
	true
	The number of books on the shelf: ["book 1", "book 2", "book 3", "book 4", "book 5", "book 6", "book 7", "book 8", "book 9", "book 10"]
	=> nil   

In this example, we can see methods with suffixes. Let's try to understand why they are added to these methods.

First, let's describe goals in which the methods are used:

 - The **books=** method is used to define new values for instances of the class;
 - The **is_full?** method is used to get boolean value - 'true' or 'false' when answering the question: "Is the address book full?";
 - The **rearrange!** method is used to change the value of instance variable @books, so that the books will in the reverse order on a shelf;
 - The **amount** method is used to output the full message of how many books are present on the shelf.


 As we see - each of the methods has its own clearly expressed meaning:

 1. The method with the exclamation mark (!) changes the state of a instance of the class, i.e. the exclamation mark indicates that the method can change the state of an instance of the class;
 2. The method with the question mark (?) - answers the question and can return either 'true' or 'false', i.e. the question mark at the end of the method name indicates that this method is a query method that returns boolean value ( 'true' or 'false');
 3. The method with equals (=) sign - assigns a value, i.e. equals '=' sign indicates that by this method we can assign a value to an instance of the class;

The method without a suffix displays the help message about the state of the shelf.

Thus, we can come to a conclusion that suffixes have a purely stylistic importance. When a programmer wants another programmer to understand more about his code - he uses these rules and it's saves time for the second developer. When you see that the method has a suffix, you can already say a few things about it. For example, if the method has ended with the question mark (?), then you can be almost sure that it will return 'true' or 'false' (if the one who wrote a code uses conventions).

It should be noted that the only suffix that affects the behavior of the method is equals suffix (=). If we use it then the method will always return the assigned value, we will talk about it a little later.


----------
## The rules about what suffixes and when to use are called conventions
The rules about what suffixes and when to use are called conventions. Ruby has a lot of conventions and this is one of them. A little later we will look into each of the conventions in detail, and for now, let's take a look what these conventions are widely used in the standard ruby library.

For instance, methods that ends with the question mark (boolean methods):

String class:

	"Hi! I'm string!".methods.select{|method| method.to_s.end_with?("?")}
produces:

	=> [:include?, :unicode_normalized?, :empty?, :eql?, :end_with?, :start_with?,
	:valid_encoding?, :ascii_only?, :between?, :instance_of?, :instance_variable_defined?,
	 :kind_of?, :is_a?, :respond_to?, :nil?, :tainted?, :untrusted?, :frozen?, :equal?]

Array class:

	["I'm", "array", "!"].methods.select{|method| method.to_s.end_with?("?")}
produces:

	=> [:include?, :any?, :empty?, :eql?, :frozen?, :all?, :one?, :none?, :member?, :instance_of?,
	:instance_variable_defined?, :kind_of?, :is_a?, :respond_to?, :nil?, :tainted?, :untrusted?,
	 :equal?]

**"Bang" methods** (methods with the exclamation mark (!)):

String class:

	"Hi! I'm string!".methods.select{|method| method.to_s.end_with?("!")}
produces:

	=> [:unicode_normalize!, :encode!, :succ!, :next!, :scrub!, :downcase!, :upcase!,
	:capitalize!, :swapcase!, :reverse!, :gsub!, :sub!, :strip!, :rstrip!, :lstrip!, :tr!,
	:chomp!, :tr_s!, :chop!, :delete!, :squeeze!, :slice!, :!]

Array class:

	["I'm", "array", "!"].methods.select { |method| method.to_s.end_with?("!") }

produces:

	=> [:uniq!, :compact!, :flatten!, :shuffle!, :map!, :reverse!, :slice!, :rotate!, :sort_by!,
	:collect!, :sort!, :select!, :reject!, :!]


**Assignment methods** (methods with the equals mark(!)):

Array class:

	["I'm", "array", "!"].methods.select{|method| method.to_s.end_with?("=")}
produces:

	=> [:==, :[]=, :===, :!=]
In this case, only the second method from the array is the assignment method (other methods are comparison methods):

	a= ["I'm", "array", "!"]
	a[0] = "Hi! Hi! Hi! I'm"
	a

produces:

	=> ["Hi! Hi! Hi! I'm", "array", "!"]   

**Safe methods** (methods without any suffixes):

Array class:

	["I'm", "array", "!"].methods.reject{|method| method.to_s.end_with?("=") || method.to_s.end_with?("!") || method.to_s.end_with?("?")}

produces:

	=> [:fill, :assoc, :rassoc, :uniq, :compact, :flatten, :to_h, :shuffle, :combination,
	:repeated_permutation, :permutation, :product, :sample, :repeated_combination, :bsearch_index,
	 :bsearch, :&, :*, :+, :-, :sort, :count, :find_index, :select, :reject, :collect, :map,
	:pack, :first, :reverse_each, :zip, :take, :take_while, :drop, :drop_while, :cycle, :insert,
	:|, :index, :rindex, :replace, :clear, :<=>, :<<, :[], :reverse, :concat, :inspect, :delete,
	:length, :size, :each, :slice, :to_ary, :shelljoin, :to_a, :to_s, :dig, :hash, :at, :fetch,
	:last, :push, :pop, :shift, :unshift, :each_index, :join, :rotate, :keep_if, :values_at,
	:delete_at, :delete_if, :transpose, :to_json, :find, :entries, :sort_by, :grep, :grep_v,
	:detect, :find_all, :flat_map, :collect_concat, :inject, :reduce, :partition, :group_by, :min,
	 :max, :minmax, :min_by, :max_by, :minmax_by, :each_with_index, :each_entry, :each_slice,
	:each_cons, :each_with_object, :chunk, :slice_before, :slice_after, :slice_when, :chunk_while,
	 :lazy, :public_send, :instance_variable_get, :instance_variable_set,
	:remove_instance_variable, :private_methods, :instance_variables, :tap, :public_method,
	:singleton_method, :extend, :define_singleton_method, :method, :to_enum, :enum_for, :=~, :!~,
	:freeze, :display, :object_id, :send, :class, :singleton_class, :clone, :dup, :itself, :taint,
	 :untaint, :untrust, :trust, :methods, :protected_methods, :public_methods,
	:singleton_methods, :__send__, :instance_eval, :instance_exec, :__id__]   


----------
## Suffixes are only part of the method name and nothing else
Suffixes are not operators or methods. We use them as additional notations that indicate what the method is doing. For example, in ruby you can find method that looks like the exclamations mark (!). It's a separate method and has nothing to do with the exclamation mark at the end of a method name.

For instance:

	!true
produces:

	=> false  

We can even redefine it (only for educational purposes :) )

	class Example

	  def !
	    puts "Do something"
	  end

	end

	new_instance = Example.new
	new_instance.!

produces:

	Do something
	=> nil


----------
## Ruby has a limited number of suffixes
The number of suffixes that you can use in ruby is limited to three: equals sign (=), exclamation mark(!), and question mark (?). If you will add something else, then you will see an error.

For example, let's try to add some suffix to the method name and let's see what happens:

	class Example

	  def do_something^
	    puts "Do something"
	  end

	end

	new_instance = Example.new
	new_instance.do_something^

produces:

	(repl):5: syntax error, unexpected '^', expecting ';' or '\n'
	(repl):9: syntax error, unexpected keyword_end, expecting end-of-input


----------
## We can work around the rules of creating suffixes using the define_method method and the send method
We can work around this rule using the define_method method and the send method. Let's see how it works. For instance, we need to add a suffix that looks like this: "^^^".

Example:

	class TestClass

	  define_method("test_method_name^^^") do
	    puts "Wow! Wow! Wow!"
	  end

	end

	puts TestClass.instance_methods(false)
	test = TestClass.new
	test.send("test_method_name^^^")

produces:

	test_method_name^^^
	Wow! Wow! Wow!
	=> nil   

As we can see, we were able to define the method that has the suffix "^^^" and even call it. We will talk about this practice and when you shoudl use it more deeply in another article.


----------
## Summary
In ruby there are conventions according to which we must use certain suffixes in the names of methods;
Suffixes are symbols in the methods names;
The number of suffixes that ruby allows to use is limited;
You can work around the rules of creating suffixes using the define_method method and the send method.

----------
##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/an-introduction-to-suffixes-in-ruby" target="_blank">https://www.alexindev.com/posts/an-introduction-to-suffixes-in-ruby</a>
