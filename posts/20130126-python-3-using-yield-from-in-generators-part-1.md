<!--
.. title: Python 3: Using "yield from" in Generators - Part 1
.. slug: python-3-using-yield-from-in-generators-part-1
.. date: 2013/01/26 09:12:00
.. tags: python, software
.. link:
.. description:
-->

A new feature in Python 3.3 is the ability to use `yield from` when defining
a generator. My current experience with Python 3 has been with Python 3.1 so
I've upgraded my installation in Ubuntu to Python 3.3 to explore "yield from".
In this tutorial, in two parts, I'm going to outline some examples and
potential use cases.

If you're already familiar with generators then you can skip the first section
of this article and continue with the specifics of "yield from" below it.

### Brief introduction to generators ###

In Python a generator can be used to let a function return a list of values
without having to store them all at once in memory. This also allows you to
utilize the values immediately without having to wait until all values have
been computed.

Let's look at the following Python 2 function:

```python
def not_a_generator():
    result = []
    for i in xrange(2000):
        result.append(perform_expensive_computation(i))
    return result
```

When we call `not_a_generator()` we have to wait until `perform_expensive_computation`
has been performed on all 2000 integers.

This is inconvenient because we may not actually end up using all the
computed results. For example, we may wish to use the above function
as follows:

```python
for element in not_a_generator():
    if not certain_condition(element):
        break
    # ...
```

Depending on the behaviour of `certain_condition`, it could be that we only
use the first 700 values returned from `not_a_generator()` and we would have
wasted time on computing the remaining values.

We can turn `not_a_generator()` into a generator by using the `yield` keyword:

```python
def my_generator():
    for i in xrange(2000):
        yield perform_expensive_computation(i)
```

### Difference between a function and a generator ###

Calling this generator is no different from our previous function:

```python
for element in my_generator():
    if not certain_condition(element):
        break
    # ...
```

The difference is that whenever the generator "yields" a value the execution
of the generator is paused and the code continues where the generator was
called. That means the value of the variable `element` is known and the execution
of the code can continue.

When the if-statement involving `not certain_condition(element)` is true then
we no longer need the generator to compute the remaining values. On the other
hand, if the for loop finishes for the current value of `element` then the
generator is executed again until it yields another value.

The above will continue until all values of the generator have been yielded or
until we no longer need the generator.

Note that we can easily get the behaviour of `not_a_generator()` back by
storing the results of `my_generator` in a list: `list(my_generator())`. This
forces the generator to be executed fully and to yield all its values.

Also note that a generator can't contain `return value` statements: using one or
more `yield` statements turns a function into a generator and you can only
use `yield` to return values to where the generator is called.

### Why yield from? ###

When a new feature is introduced in a programming language we should ask
ourselves if and why this was necessary. The short explanation is that it
enables you to easily refactor a generator by splitting it up into multiple
generators.

For basic purposes we can use plain generators to compute values and to
pass those values around. The benefits of `yield from` should become clear
when we know what it does and in which situations it can be used.

Consider a generator that looks like this:

```python
def generator():
    for i in range(10):
        yield i
    for j in range(10, 20):
        yield j
```

As expected this generator yields the numbers 0 to 19. Let's say we wish
to split this generator into two generators so we reuse them elsewhere.

We could rewrite the above into:

```python
def generator2():
    for i in range(10):
        yield i

def generator3():
    for j in range(10, 20):
        yield j

def generator():
    for i in generator2():
        yield i
    for j in generator3():
        yield j
```

This version of `generator()` also yields the numbers 0 to 19. However, it
feels unnecessary to specify that we wish to iterate over both `generator2` and
`generator3` and yield their values. This is where `yield from` comes in.
Using this new keyword we can rewrite `generator` into:

```python
def generator():
    yield from generator2()
    yield from generator3()
```

This gives the same result and it is much cleaner to write and maintain. It is
also quite similar to the way functions are refactored and split up into
multiple functions. For example,  a large function can be split into several
smaller functions, `f1()`, `f2()` and `f3()`, and the original function simply
calls `f1()`, `f2()` and `f3()` in sequence.

### Useful situations for 'yield from' ###

Those of you familiar with the `itertools` module may note that the above example
is rather simple and does not truly justify introducing a new keyword in the language.

Using the `chain` function from the `itertools` module we also could have written:

```python
from itertools import chain

def generator():
    for v in chain(generator2(), generator3()):
        yield v
```

It can be argued that the `yield from` syntax and semantics are slightly cleaner
than importing an additional function from a module but, leaving that aside,
we have not yet seen an example where `yield from` has enabled us to do something
new. As we will see later in this tutorial, the main benefit of `yield from` is
to allow easy refactoring of generators.

It should be noted that it is not necessary for new programming language syntax
to also introduce new semantics (i.e, to express something that was not
possible before).

Many languages introduce syntax, often called
syntactic sugar, to make it easier to write something that would otherwise be
cumbersome to write. For example, Haskell allows you to easily write a string as
`"example"` which is shorthand syntax for `['e', 'x', 'a', 'm', 'p', 'l', 'e']`
(a list of characters). Cleaner and more maintainable code can suffice to
introduce new syntax into a programming language.

### Binary tree example ###

[The proposal](http://www.python.org/dev/peps/pep-0380/) that introduces
the `yield from` syntax provides a few examples to
demonstrate the new behaviour. One of them is a basic binary tree and we
can traverse the nodes of the tree using normal for loops as well as the
new `yield from` syntax.

In the next part of this tutorial we will build upon this example to explain
the benefits of `yield from`.
