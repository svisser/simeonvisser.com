<!--
.. title: On Modularity in Software
.. slug: on-modularity-in-software
.. date: 2014/05/24 20:00:00
.. tags: python, software
.. link:
.. description:
-->

In 2009 I started working on a crossword editor written in Python called
[Palabra](https://bitbucket.org/svisser/palabra) (Spanish for "word"). It
was a desktop application developed for Ubuntu Linux that allowed anyone to
construct a crossword grid, fill in words and enter the clues for each entry.
It also included various operations on the grid, such as shifting cells, decorating
a cell and viewing grid statistics.

To create a crossword the application needs to provide all those things: a visual editor displaying
the grid, the ability to load and manipulate lists of words and so on.
Implementing everything in the same codebase seemed like a sensible approach
at the time. But looking
back at the code five years later it has become apparent that there is a much better
approach to constructing the same application: by splitting the functionality
into multiple modules that can be released and developed independently.
This approach is useful for very large software projects but it also applies to
seemingly small applications such as a crossword editor.

For example, the application needs to have the ability to read and write the
crossword to a file format. At the time (and still today) there are various
file formats in use in the crossword community that a crossword editor could
support. This includes the .puz file format, the .jpz file format and the
[.ipuz file format](http://www.ipuz.org).

It's possible to include the read / write functionality for multiple file formats as part of
the application's code. After all it makes little sense to use a
crossword editor if you can't save your work. But this
functionality can be reused in other applications or libraries without
needing the full code of the crossword editor. One can imagine a Python
library for .puz files, a Python library for .jpz files and a Python library
for .ipuz files which can be maintained independently. The
Python library for the .ipuz file format didn't exist yet so I have started
to [work on that](https://github.com/svisser/ipuz) ([entry on PyPI](https://pypi.python.org/pypi/ipuz)).
These modules can then be used by the Palabra crossword editor.

Taking this one step further we can now explore how a monolithic crossword
editor can be split up into multiple modules:

- A module for reading / writing / validating .puz files.
- A module for reading / writing / validating .jpz files.
- A module for reading / writing / validating .xpf files.
- A module for reading / writing / validating .ipuz files.
- A module with a canonical data structure for a crossword grid plus common operations on the grid.
- A module for filling a crossword grid with words, either manually, semi-assisted or fully-automated.
- A module for loading and processing word lists (including fast searches, such as "give me all five-letter words with an S in the fourth position").

Each of these modules can be useful for other applications or purposes. It also
means that the desktop crossword editor needs to do no more than expose the functionality
of the above modules to the user. For example, it is possible to create a
web-based crossword editor by reusing all the Python modules and by
reimplementing only the logic of the editor.
