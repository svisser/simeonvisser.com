<!--
.. title: Using 'else' in Python
.. slug: using-else-in-python
.. date: 2013/02/05 11:00:00
.. tags: python, software
.. link:
.. description:
-->

Python allows you to use the 'else' keyword in places where most other
programming languages don't. I assume that you're familiar with the
basic `if/else` where the block after the 'else' is executed when the
condition in the if-statement isn't true.

In Python you can also add 'else' to a `for` loop, a `while` loop as well
as a `try/except` section. I was aware of the ability to use 'else' in combination with a for
loop but I only learned of the `try/except/else` combination a few days ago
so perhaps it's worth spreading the word about this.

### Using 'else' in combination with 'if' ###

For completeness it's important to discuss the basics of the `if/else` first
as the other uses of 'else' in Python differ slightly differently from this.

<pre>
if condition:
    # executed when condition == True
    ...
else:
    # executed when condition == False
    ...
</pre>

Despite the familiarity of this construct, it's worth noting that the reason
why the `else` block is executed is explicitly mentioned in the
code (i.e., the condition in the if-statement).

For the other uses of 'else'
you'll see that this isn't the case. You'll need to have read a Python guide
(or this article) to know when those 'else' blocks are executed.

### Using 'else' in combination with 'for' ###

This is where things become interesting as the `else` statement can lead
to cleaner code. It frequently happens that we wish to iterate over a collection
of objects and when a certain condition is met we `break` from the loop.
For example:

<pre>
for car in cars:
    if needs_repair(car):
        send_for_repair(car)
        break
</pre>

In this example we iterate over a collection of cars and we stop when we've
found a car that needs a repair. No further cars will be examined when the
first broken car has been found.

Now, what if we wish to take a certain action
when we haven't found any car for repair? We could introduce a
variable `car_found_for_repair` for this purpose:

<pre>
car_found_for_repair = False
for car in cars:
    if needs_repair(car):
        send_for_repair(car)
        car_found_for_repair = True
        break
if not car_found_for_repair:
    # do something
    ...
</pre>

Python allows an elegant solution by adding an `else`
statement to the for loop. The block of code after the `else` is executed when
we have *not* performed a `break` in the loop. The code now looks as follows
and it behaves the same:

<pre>
for car in cars:
    if needs_repair(car):
        send_for_repair(car)
        break
else:
    # do something
    ...
</pre>

In other words, when the for loop completes successfully (i.e., without
being exited by a `break` statement) the `else` section is executed. But
when we break from the loop at some point then that section won't be executed.

### Using 'else' in combination with 'while' ###

This is feature in the Python language that I only discovered when
reading the [grammar specification](http://docs.python.org/3/reference/grammar.html)
of the language. This construct may not be that popular or, at least, I have not
seen it in the Python code that I have read over the years.

Similar to the `for` loop you can add an `else` statement to a `while` loop
and it'll be executed when a `break` has *not* been performed in the loop.
I'll leave the example out as it would be similar to the above one.

### Using 'else' in combination with 'try' and 'except' ###

The basics of a `try/except` section in Python consist of:

<pre>
try:
    # run some code
except ValueError:
    # catch exception when it is raised
</pre>

This means the code within the `try` is executed and when a
`ValueError` is raised execution continues in the `except` block. Some of you
will know that a `finally` block can be added to execute code after a
`try/except` regardless of whether an exception was raised:

<pre>
try:
    # run some code
except ValueError:
    # run this when ValueError is raised
finally:
    # run this after the code and any exception handling code
</pre>

Similar to the `for` loop example above, what if we wish to execute some code
when *no* exception has been raised? We could introduce another boolean variable
for that purpose but Python allows you to add an `else` block:

<pre>
try:
    # run some code
except ValueError:
    # run this when ValueError is raised
else:
    # run this only when no exception is raised
finally:
    # run this after the code and any exception handling code
</pre>

The `else` section is only executed when no exception at all is raised.

In this example we are only catching `ValueError` exceptions. When, for example,
an `IndexError` is raised then Python will run the `try` block up until the
exception, the `finally` block and the `ValueError` won't be caught (yet).
