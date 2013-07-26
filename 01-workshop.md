% Scala Workshop - Intro
% Jim Baker


Goals for workshop
==================

* Fast paced! No prisoners! Best experience ever!
* Brief introduction to some essentials of Scala programming
* You still need to learn the rest afterwards! (A journey of a lifetime...)
* Combination of short lectures, followed by various group exercises


Resources
=========

* Programming in Scala, 2nd Edition http://www.artima.com/shop/programming_in_scala_2ed (first edition is available for free)
* Functional Programming in Scala http://www.manning.com/bjarnason/
* Martin Odersky, Functional Programming Principles in Scala, Coursera https://www.coursera.org/course/progfun
* Use the source, Luke! https://github.com/scala/scala
* Good docs, with links to the source: http://www.scala-lang.org/api/


Introductions
=============

* My background
* Your background
* Time to pair up (or into triples)


Get ready: group exercise
=========================

* Download and install Scala 2.10.2 from http://www.scala-lang.org/downloads
* Bring up a Scala console


`scala` package
===============

`scala` package provides the equivalent of Python's builtins; per the docs:

> Core Scala types. They are always available without an explicit import.

http://www.scala-lang.org/api/current/index.html#scala.package


Scala, in the console
=====================

* Scala interpreter: scala
* Can run Scala scripts on command-line:  
  `$ scala script.scala`

* Scala applications, at the command line or in script

````scala
object M {  
  val x: Int = 3  
}
````

* Think of `M` like a module.  Can say: `M.x`
* Singleton


Hello, world!
=============

Or entry points:

````scala
object HelloWorld {
  def main(args: Array[String]) {
    println("Hello, world!")
  }
}
````

Scala
=====

Let's look at some examples of the following:

* Literals
* Types - any value has a type
* Values, variables
* Expressions - operations "waiting to be evaluated"
* We will get to classes, eventually


Group question
==============

True or false: this is valid Scala code:

````scala
var aString = "abc"
aString += "123"
````

Tuples
======

````scala
val onet: (Int, Boolean) = (1, true)
val projectOne = onet._1
val projectT = onet._2
````

NB: projection is the usual way of describing this in relational databases


Function basics
===============

* Frequently use `def` to define functions
* Arguments must have types, return type can be occasionally inferred; good practice is to declare the return type
* And we require it!


Functions
=========

````scala
def divRem(x: Int, y: Int): (Int, Int) = 
    (x / y, x % y) 
val testDivRem = divRem(7,3)
````

NB: returns a tuple


Function values
===============

But can also use other approaches to define anonymous functions, then assign:

````scala
def square(x: Int):Int = x * x
val square: Int => Int = (x: Int) => x * x
var f: Int => Int = (x: Int) => x * x
f = (x: Int) => 2 * x
````

Pattern matching
================

````scala
	val (one, t) = onet
	val surpriseOne, surpriseT = onet
````

NB: unpacking like this is more idiomatic than the use of `._N` accessors


Unit values
===========

````scala
val u: Unit = ()
````

* Think of this as a zero-length tuple (that would the Python equivalent); or of `void`
* When is this value at all useful?


Lists
=====

* Lists are of a given type, eg `List[Double]`
* Use `::` (read as cons) to construct a `List[T]` of a head element of type `T` and a tail list of `List[T]`
* `::` associates right (why?)


Group question
===============

* True or false: `42 :: 0` is a valid `List[Int]`.
* Why?


Factorial definition
====================

~~~~
factorial(n) = 1 * 2 * 3 * ... * n
~~~~


Factorial, imperatively
=======================

````scala
def factorial(n: Int): Int = {
  var i = n
  var acc: Int = 1
  while (i > 0) {
    acc *= i
    i -= 1
  }
  return acc
}
````


Factorial, recursively
======================

````scala
def factorial(n: Int): Int =
  if (n == 0)
    1
  else
    n * factorial(n - 1)
````

NB: note the implict return


Pattern matching alternative
============================

Introduce `match` and `case`:

````scala
def factorial(n: Int): Int = n match {
  case 0 => 1
  case _ => n * factorial(n - 1)
}
````


How can we deal with invalid args in Scala?
===========================================
  
* For example
    `factorial(-2)`

* use `require`

````scala
def factorial(n: Int): Int = {
  require(n >= 0)
  n match {
    case 0 => 1
    case _ => n * factorial(n - 1)
  }
}
````


What introduces new scopes in Scala?
====================================

Comprehensive list:

* {}
* functions
* match-case expressions
  

Scoping rules for functions
===========================

````scala
def plusFour(y: Int): Int = y + four
val eight = plusFour(four)
````

More scoping
============

````scala
val y = 28
def plusWhat(y: Int): Int = 
  y + {
    val y = 17
    y
  }
println(plusWhat(y+1))
````

What's printed?


Group exercise: Ackermann's Function
====================================

Ackermann's function has the following definition:

$A(m, n) = \begin{cases}
   n+1               & \text{if } m = 0 \\
   A(m-1, 1)         & \text{if } m > 0 \text{ and } n = 0 \\
   A(m-1, A(m, n-1)) & \text{if } m > 0 \text{ and } n > 0
\end{cases}$


Superexponential growth!
========================

$m$ / $n$ 0             1                   2                     3                            $n$
--------- ------------- ------------------- --------------------- ---------------------------  ------------------------------------
0         1             2                   3                     4                            $n + 1$
1         2             3                   4                     5                            $n + 2$
2         3             5                   7                     9                            $2n + 3$
3         5             13                  29                    61                           $2^{(n+3)} - 3$
4         $2^{2^{2}}-3$ ${2^{2^{2^{2}}}}-3$ $2^{2^{2^{2^{2}}}}-3$ ${2^{2^{2^{2^{2^{2}}}}}}-3$  $\begin{matrix}\underbrace{{2^2}^{{\cdot}^{{\cdot}^{{\cdot}^2}}}} - 3 \\n\mbox{ + 3}\end{matrix}$


http://en.wikipedia.org/wiki/Ackermann_function


Skeleton for `ack`
==================

````scala
def ack(m: Int, n: Int): Int = {
	require(                           )

    (m, n) match {

	
	
	
	


    }
}
````


More problems for the group
===========================

Implement and test the following:

```scala
def xor(a: Boolean, b: Boolean): Boolean =
  throw new UnsupportedOperationException
def repeat(s: String, n: Int): String = 
  throw new UnsupportedOperationException
````
