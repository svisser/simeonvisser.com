<!--
.. title: How Does 'from __future__ import ...' Work in Python?
.. slug: how-does-from-future-import-work-in-python
.. date: 2013/05/27 00:18:00
.. tags: python,software-development,programming-languages,parsers
.. link:
.. description:
-->

A few days ago I learned that `from __future__ import barry_as_FLUFL` allows you
to use the `<>` operator again for inequality in Python 3.3:

    >>> 3 <> 4
      File "<stdin>", line 1
        3 <> 4
           ^
    SyntaxError: invalid syntax
    >>> from __future__ import barry_as_FLUFL
    >>> 3 <> 4
    True

[PEP 401](http://www.python.org/dev/peps/pep-0401/)
details the history behind this import.

Those familiar with
Python will know that the `__future__` module is used to make functionality
available in the current version of Python even though it will only be officially
introduced in a future version.

For example, `from __future__ import with_statement` allows you to use the
[with statement](http://www.python.org/dev/peps/pep-0343/) in Python 2.5 but it
is part of the language as of Python 2.6.

The syntax `from module import function` generally means that the function
from the specified module is made available in the current scope and that it
can be called.

But the earlier examples demonstrate that importing can also
make new operators or keywords available. This poses the question of
how this module actually works as it is somehow different from a normal
import: Python does not allow anyone to implement new operators or keywords
so how can we import them from a seemingly normal module `__future__`?

### How does it work? ###

Let's have a look at the [source of the future module](http://hg.python.org/cpython/file/3.3/Lib/__future__.py).
It turns out that anything you can import from `__future__` has been hardcoded
into the language implementation. Each import is specified using a `_Feature`
object that records the versions in which the new feature is available (using the
import and officially without the import) and also a special compiler flag.

Calling `repr()` on the imported object also shows this:

    >>> repr(barry_as_FLUFL)
    "_Feature((3, 1, 0, 'alpha', 2), (3, 9, 0, 'alpha', 0), 262144)"

Each of these compiler flags is a constant that is also stored in
[compile.h](http://hg.python.org/cpython/file/3.3/Include/compile.h). This won't
tell us much as it merely defines the available imports from `__future__`.

So let's look at the actual code that analyses the code for future language
features, which is in [future.c](http://hg.python.org/cpython/file/3.3/Python/future.c).
Most importantly this file defines a function called `PyFuture_FromAST` which
analyses the code and builds a `PyFutureFeatures` object that records which
imported functionality from `__future__` is needed.

### This is not a normal module ###

We can now see why, although similar in syntax, the `__future__` module behaves
differently from normal imports.

As the new operators and keywords need to be
recognized when parsing the Python source code it is necessary for Python to
be aware of the 'futuristic imports' at a lower level than at the level of
regular imports.

The abbreviation *AST* that we saw in the name
of `PyFuture_FromAST` refers to [Abstract Syntax Tree](http://en.wikipedia.org/wiki/Abstract_syntax_tree)
and this is precisely the level at which Python needs to know which operators
and keywords are available: a source file is analysed, converted into an Abstract
Syntax Tree and this data structure is then converted into bytecode which can
be executed.

I think this sums up why importing from `__future__` is different from other
modules. One can also envision a language where operators and keywords can be
defined in the language itself and then a `__future__` module would import those
as any other function or object.

But Python is not such a language. As a result the new operators or keywords
are baked into the implementation and they can be made available using a
special `__future__` module.
