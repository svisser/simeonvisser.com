<!--
.. title: List Comprehensions Are For Lists
.. slug: list-comprehensions-are-for-lists
.. date: 2013/05/08 12:44:00
.. tags: python, software
.. link:
.. description:
-->

List comprehensions in Python are a great way of expressing a list but, as
the name suggests, they are for lists and not purely for iterating over an
iterable and calling some method.

If you find yourself writing:

<pre>
[obj.some_method() for obj in my_objects]
</pre>

then you actually intended to write:

<pre>
for obj in my_objects:
    obj.some_method()
</pre>

I know, the first is a one-liner which feels exotic compared to an old-school
for loop.

But the second version expresses what you want to
do: call a method on each object and ignore the return value of that method.
In the first version you're constructing a list, storing the return values and
then forgetting about the list altogether because you're not assigning to
a variable.

In CPython 3.3 both cases produce similar bytecode but a list is still constructed
unneccessarily. Given that Python is designed for readability the second one expresses
better what you're doing. If you do need to store the return values in a list
then you can rewrite the code later.

Similarly, the same argument applies to the following construct:

<pre>
map(lambda obj: obj.some_method(), my_objects)
</pre>

If you need the constructed list of return values then you can rewrite it as
a list comprehension. If you don't need the list then you can rewrite it as a
for loop.
