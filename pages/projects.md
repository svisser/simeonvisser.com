<!--
.. title: Projects
.. slug: projects
.. date: 2012/02/04 10:00:00
.. tags:
.. link:
.. description:
-->

An overview of some of my personal digital projects:

## Photography

You can find my photos on [500px.com](http://500px.com/simeonvisser).

## logaugment (2014)

logaugment is a Python library for specifying default values to be used
in logging calls. This allows you to add, set or remove defaults for keys
that may be used in formatted logging strings. The library makes it convenient
to specify defaults and prevents accidental exceptions in case a logging call
doesn't pass a necessary value that the logger expects.

The project supports Python 2.7+ and 3.1+.

* [Website](https://github.com/svisser/logaugment)
* [Package at PyPI](http://pypi.python.org/pypi/logaugment)

## Porting puzpy to Python 3.x (2014)

In 2014 I spent some time porting the package puzpy (a Python library
for parsing .puz crossword files) to support Python 3.x. As a file parsing
library the changes were predominantly a cleaner Unicode / bytes separation.

The project supports Python 2.7+ and 3.2+.

* [Website](https://github.com/alexdej/puzpy)
* [Package at PyPI](http://pypi.python.org/pypi/puzpy)

## ipuz (2014)

ipuz is a Python library to read and write the
[ipuz file format](http://ipuz.org/). The ipuz file format is JSON and supports
the specification of various puzzles. This Python library performs loading
and validation of such JSON data.

A partial implementation of the specification could already be found in
my crossword editor Palabra (see below). The ipuz library supports all
official puzzle specifications as opposed to only crossword puzzles.

The project supports Python 2.7+ and Python 3.2+.

* [Website](https://github.com/svisser/ipuz)
* [Package at PyPI](http://pypi.python.org/pypi/ipuz)

## simeonvisser.com (2010 - )

This website initially ran on a homebuilt file-based content management system
written in PHP using [Bootstrap](http://twitter.github.com/bootstrap/)
and [Markdown](https://en.wikipedia.org/wiki/Markdown).

In March 2013 the site was converted to use the Python static site generator
[Nikola](http://nikola.ralsina.com.ar) with content written in Markdown
and styled using Bootstrap.

* [Source](https://github.com/svisser/simeonvisser.com)

## Palabra (2009 - 2011)

Palabra is a free crossword editor for Ubuntu Linux with a range of features, including
visual editing tools (e.g., grid symmetry options), grid patterns, word lists, clue editing
and export to PDF. Interesting components of the program are: computing the statistics
of a crossword grid as well as some work towards an automated / assisted
grid filling engine.

Palabra is a desktop application written in Python and C, using [GTK](http://www.gtk.org/), [Cairo](http://cairographics.org/) and [Pango](http://www.pango.org/).

* [Website](https://bitbucket.org/svisser/palabra/)
* [Package at PyPI](http://pypi.python.org/pypi/palabra)

<img src="/images/palabra_1.png" alt="" />

## egdieren.nl (2007 - 2013)

[egdieren.nl](http://egdieren.nl/) is a website written from scratch in PHP.
I developed both the front-end and back-end using HTML, CSS and
JavaScript (mostly [jQuery](http://jquery.com/)). The design of the website
was provided. Maintenance of the site was handed over in 2013.

<img src="/images/egd_1.png" alt="" />

## Video game maps and add-ons (2002 - 2007)

I've created various levels and add-ons for video games in several genres, notably
first-person shooters and role-playing games.

This includes the design and development
of the virtual environments using the level editing tools of the games
(e.g., UnrealEd of the [Unreal Engine](http://www.unrealengine.com/)).
Additionally, I've written scripts for interactive in-game events, dialogs and guided cutscenes.

With a few exceptions, all of the content (textures, 2D and 3D models) comes
from the game itself or the game's fan community.

<i>Unreal Tournament 2004 - VCTF-LavaValley-V2</i><br />
<img src="/images/ut2004_1.jpg" alt="" />
<hr />

<i>Unreal Tournament 2004 - DM-Tehan</i><br />
<img src="/images/ut2004_2.jpg" alt="" />
<hr />

<i>Dink Smallwood - Once in a Lifetime</i><br />
<img src="/images/rpg_1.png" alt="" />
<hr />

<i>Dink Smallwood - Blood Scorpions</i><br />
<img src="/images/rpg_2.png" alt="" />
<hr />
