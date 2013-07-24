Scala workshop notes
====================

A work in progress, with pull requests gladly accepted for typos,
errata, diagrams, and other improvements.

You can simply look at these files online using github's support for
markdown. Otherwise, I've included `generate`, a simple script that
wraps pandoc with some standard options I use. To build a presentation
PDF:

    $ ./generate 01-kickoff.md
	
You will need to install the dependencies for pandoc. For Ubuntu, these are:

    sudo apt-get install pandoc
	sudo apt-get install latex-beamer

For OS X, follow the instructions at
http://johnmacfarlane.net/pandoc/installing.html. In addition, make
sure you also install the MacTeX-Additions as linked from
http://www.tug.org/mactex/morepackages.html


Help for generate
=================

~~~~
$ ./generate  --help
usage: generate [-h] [--format FORMAT] [--incremental] source

Simple driver for pandoc. Or use pandoc directly for more options.

positional arguments:
  source             Source markdown file

optional arguments:
  -h, --help         show this help message and exit
  --format FORMAT    Output format
  --incremental, -i  Incremental display of lists
~~~~
