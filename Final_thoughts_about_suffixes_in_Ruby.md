##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/final-thoughts-about-suffixes-in-ruby" target="_blank">https://www.alexindev.com/posts/final-thoughts-about-suffixes-in-ruby</a>
----------
##### This article is written for ruby version 2.3.1. In other versions, some things may differ.
----------
## Final thoughts about suffixes in Ruby

In this article, we will consider ending thoughts about how suffixes are used in ruby methods.

----------
This article is divided into the following sections:

 1. [To use conventions or not - your choice](#)
 2. [Even in the standard library you can find methods which not follow conventions](#)
 3. [Various libraries may not follow the conventions](#)
 4. [Summary](#summary)
----------
## To use conventions or not - your choice
The use of suffixes is a convention in ruby, which means that you can not use them. However, it will be great if use them. It will give more expressiveness to your code and as a result, other programmers will spend less time investigating your code.


----------
## Even in the standard library you can find methods which not follow conventions
For example there are methods like pop and shift. They don't have the exclamation mark at the end of the method name, however, when we use them - we mutate the object.

Method pop:

	a = ["111", "222", "333"]
	puts a.object_id
	a.pop
	puts a.inspect
	puts a.object_id

produces:

	47323636775420
	["111", "222"]
	47323636775420
	=> nil   

As you can see, the array was changed. First, we created an array with 3 elements, and after we used method pop we got the array with only 2 elements in it. The fact that we end up having the same object is confirmed by the fact that it has the same object id that was in the beginning. According to conventions for methods naming , the pop method should have the exclamation mark at the end of the method name, but it doesn't.

Another example, the shift method:

	a = ["111", "222", "333"]
	puts a.object_id
	a.shift
	puts a.inspect
	puts a.object_id

produces:

	47430277945520
	["222", "333"]
	47430277945520
	=> nil   

As we see, we have the same identity here. Thus, you should be careful when using the standard library methods, if the method doesn't have the exclamation mark at the end of the method name, this doesn't mean that it's not a mutator method. The same conclusion is true for methods with other suffixes. In most cases, the methods will satisfy the requirements of the conventions, but it's better to check first and then use a method more properly. A good practice is to take a look at the documentation and test the method through irb, then you will definitely know what the method does.


----------
## Various libraries may not follow the conventions
It's not a secret that different libraries were written by different programmers and who knows how they used the suffixes.  In some libraries (for example, in rails) there are can be methods with the exclamation mark which can has a different meaning (for example, the beng method can tell if we will see an error message after using this method). Thus, as in the case of the standard ruby library, methods should be checked.



----------
## Summary:
 - use of suffixes allows you to write better code
 - don't trust usages of suffixes in ruby libraries (even when you work with standart ruby library) - check what you use

----------

##### Latest version of this article you can find here:  <a href="https://www.alexindev.com/posts/final-thoughts-about-suffixes-in-ruby" target="_blank">https://www.alexindev.com/posts/final-thoughts-about-suffixes-in-ruby</a>
