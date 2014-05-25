<!--
.. title: Python 3: Using "yield from" in Generators - Part 2
.. slug: python-3-using-yield-from-in-generators-part-2
.. date: 2013/02/04 12:00:00
.. tags: python, software
.. link:
.. description:
-->

In the previous part of this tutorial,
I have discussed the basics of generators, some differences between functions
and generators. I have also hinted at some benefits of the new `yield from` syntax.
I recommend reading that part of the tutorial first before continuing unless
you're well familiar with generators in Python.

All examples in this part of the tutorial will be in Python 3.3 unless
stated otherwise.

### Basic binary tree ###

In this second part I'll build upon a basic binary tree example that
demonstrates the uses of the `yield from` syntax. For the sake of this example,
I'll let each node in the tree have a list of children rather than, as is often
done, let each node point to its parent node.

Here is the implementation of this data structure without the new syntax:

```python
class Node:

    def __init__(self, value):
        self.left = []
        self.value = value
        self.right = []

    def iterate(self):
        for node in self.left:
            yield node.value
        yield self.value
        for node in self.right:
            yield node.value

def main():
    root = Node(0)
    root.left = [Node(i) for i in [1, 2, 3]]
    root.right = [Node(i) for i in [4, 5, 6]]
    for value in root.iterate():
        print(value)

if __name__ == "__main__":
    main()
```

As expected, this prints the values `1`, `2`, `3` (of the left children),
the value `0` (of the root node) and the values `4`, `5`, and `6` (of the
right children).

However, this example only iterates over the root node
and its children; it won't recursively iterate over the children of the
child nodes (if there happened to be any). Let's modify the `iterate()`
method to make that happen:

```python
def iterate(self):
    for node in self.left:
        for value in node.iterate():
            yield value
    yield self.value
    for node in self.right:
        for value in node.iterate():
            yield value
```

This version also calls `iterate()` on each of the child nodes so this
yields each of the nodes in the tree. This code is rather cumbersome: for
each of the left and right children we yield all the values by using
explicit iteration. This is where `yield from`
simplifies the code:

```python
def iterate(self):
    for node in self.left:
        yield from node.iterate()
    yield self.value
    for node in self.right:
        yield from node.iterate()
```

### Other aspects of generators ###

Now, the story would be over pretty quickly if that was all there was to it.
To appreciate `yield from` I'll need to explain an alternative way of using
a generator.

A generator can be controlled using methods such as `send()` and `next()`. These
and related methods allow you to start, stop and continue a generator rather
than having Python handle most of the generator's execution.

For example, instead of the above basic loop:

```python
for value in root.iterate():
    print(value)
```
    
we can also write:

```python
it = root.iterate()
while True:
    try:
        print(it.send(None))
    except StopIteration:
        break
```

The `send()` method allows you to "send" a value into the generator, which means
the `yield` expression receives that value. That value can be used by assigning
it to a variable (i.e., `v = yield self.value`).

In this case we repeatedly send
the value `None` into the generator and our generator doesn't utilize the
value that it sent into it. Effectively this leads to the same result as
the previous loop that prints the generator's yielded values.

### Benefits of yield from ###

The primary benefits of `yield from` come when you've written a generator that
uses these techniques and when it needs to be refactored. This means you'll have
to subdivide the generator into multiple subgenerators and send / receive values
to and from those subgenerators. Rather than rewriting
the generator to send values to the subgenerator, you can simply use
`yield from` and the semantics will remain the same.

There are some caveats and some situations which aren't handled but that's beyond
the scope of this tutorial (I'll refer
you to [the official proposal](http://www.python.org/dev/peps/pep-0380/) for details).

### Another example generator ###

Let's create a small generator that demonstrates the above:

```python
def node_iterate(self):
    for node in self.left:
        input_value = yield node.value
        # ...
    input_value = yield self.value
    # ...
    for node in self.right:
        input_value = yield node.value
        # ...
```

This generator only iterates over the node and its immediate children. Any
value sent into the generator is stored in the variable `input_value`
which is then available for computations (the `# ...` sections).

For example, the code that uses this generator may perform various
computations and it passes intermediate values back into the generator so
that they can be used there. The following shows how the yielded values
are summed and the subtotals are passed back into the generator:

```python
total = 0
it = root.node_iterate()
it.send(None)
while True:
    try:
        value = it.send(total)
        total += value
    except StopIteration:
        break
```

### Refactoring iteration over children ###

It seems repetitive to have the same code for both the left and right
children so we can refactor that into:

```python
def child_iterate(self, nodes):
    for node in nodes:
        input_value = yield node.value
        # ...

def node_iterate(self):
    yield from self.child_iterate(self.left)
    input_value = yield self.value
    # ...
    yield from self.child_iterate(self.right)
```

Note that it is not recommended practice to call a class method
(`self.child_iterate`) by passing an instance variable (i.e., the
`self.left` and `self.right` arguments) but
that is a topic for a different post. The important part is that we can only
refactor in this way as a result of the new `yield from` semantics.

When we send values into `node_iterate()` they are automatically
passed into the subgenerator `child_iterate()` where `input_value`
receives a value.

Prior to Python 3.3, if we had written:

```python
for value in self.child_iterate(self.left):
    yield value
```

then this would yield values from `child_iterate()` but `input_value` would
remain `None`. To make it work as expected, we would need to explicitly `send()`
the values from `node_iterate()` into `child_iterate()`.

### More refactoring ###

Lastly, we can perform one more refactoring step leaving us with only one
section where the `input_value` is received and used:

```python
def process(self):
    input_value = yield self.value
    # ...

def child_iterate(self, nodes):
    for node in nodes:
        yield from node.process()

def node_iterate(self):
    yield from self.child_iterate(self.left)
    self.process()
    yield from self.child_iterate(self.right)
```

As you become more familiar with using `send()` and related functions, you'll
notice that `yield from` makes life easier. I suggest reading the exact
semantics in the [specification](http://www.python.org/dev/peps/pep-0380/)
to know when and how you'll be able to use it.
