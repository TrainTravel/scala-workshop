lecture-notes
=============

Course lecture notes for Spring 2013 of CSCI 3155. A work in progress, with pull requests gladly accepted for typos, errata, diagrams, and other improvements, all for extra credit.

You can simply look at these files online using github's support for markdown. Otherwise, I've included `generate`, a simple script that wraps pandoc with some standard options I use. To build a presentation PDF:

    $ ./generate 01-kickoff.md
	
You will need to install the dependencies for pandoc. For Ubuntu, these are:

    sudo apt-get install pandoc
	sudo apt-get install latex-beamer

Help for generate:

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
