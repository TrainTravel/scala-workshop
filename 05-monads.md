Software as "Lego blocks"
=========================

* Ideal: be able to write reusable code and snap together ("compose" or "combine")
* What would it take?
* Can we support in the PL itself?


Composition and design patterns
===============================

* Observer
* Proxy
* Iterator
* Adapter
* etc etc

http://c2.com/cgi/wiki - the **original** wiki


Observations
============

* Composition seems to be key
* Can we manipulate the composition?
* Can we exploit higher order functions to do so?


Monads by example
=================

* Monads are... elephants, burritos, astronauts, something or another
* Not really!
* A way of structuring computations that support a specific pattern of usage
* Formal algebra underpinnings (hence the name "monad")


Formalisms
==========

> "a monad is a monoid in the category of endofunctors, what's the problem?"


PL humor
========

A Brief, Incomplete, and Mostly Wrong History of Programming Languages:

> 1990 - A committee formed by Simon Peyton-Jones, Paul Hudak, Philip Wadler, Ashton Kutcher, and People for the Ethical Treatment of Animals creates Haskell, a pure, non-strict, functional language. Haskell gets some resistance due to the complexity of using monads to control side effects. Wadler tries to appease critics by explaining that "a monad is a monoid in the category of endofunctors, what's the problem?"

http://james-iry.blogspot.com/2009/05/brief-incomplete-and-mostly-wrong.htlm


Other structures we have seen
=============================

* Functions
* Classes, objects, etc
* How can we combine?


Combination
===========

> As far as programming is concerned, a monad is just a particular
> style of combinator library. That is, one which supports a few basic
> means of combination.

http://www.haskell.org/haskellwiki/Monads_as_computation


Problem: working with missing values
====================================

* How to solve in language X?
* Java `null`, Python `None`
* Need to consider differences with respect to primitive types (where's the issue here?)
* `Option` (or `Maybe` in Haskell)


Compare Java
============

````java
public boolean __exit__(ThreadState ts,
       PyException exception) {
  final PyObject type, value, traceback;
  if (exception != null) {
    type = exception.type;
    value = exception.value;
    traceback = exception.traceback;
  } else {
    type = value = traceback = Py.None;
  }
  return __exit__method.__call__(ts, type,
    value == null ? Py.None : value,
    traceback == null ? Py.None : traceback).__nonzero__();
}
````

from org.python.core.ContextGuard in Jython source


Compare Groovy: Null object pattern
===================================

Groovy supports the `?.` operator, which is null-aware:

````groovy
people << new Person(name:'Harry')
biggestSalary = people.collect{
  p -> p.job?.salary }.max()
println biggestSalary
````

`?.` supports chaining of references, terminating on the last non-null
reference - or null

http://groovy.codehaus.org/Null+Object+Pattern


`Option` in Scala
=================

Removed the use of explicit conditional logic:

```scala
val name: Option[String] =
  request getParameter "name"
val upper = name map { _.trim } filter
  { _.length != 0 } map { _.toUpperCase }
println(upper getOrElse "")
````

http://www.scala-lang.org/api/current/index.html#scala.Option


Group exercise: Scala koan
==========================

* AboutOptions



Alternative
===========

Using `for` "sugaring":

````scala
val upper = for {
  name <- request getParameter "name"
  trimmed <- Some(name.trim)
  upper <- Some(trimmed.toUpperCase)
    if trimmed.length != 0
} yield upper
println(upper getOrElse "")
````


Pattern matching variant
========================

````scala
val nameMaybe = request getParameter "name"
nameMaybe match {
  case Some(name) =>
    println(name.trim.toUppercase)
  case None =>
    println("No name value")
}
````


Manipulating with `lift`
=========================

* `lift` is a method of `PartialFunction` objects
* Converts to a normal function, using `Option[T]`


Example
=======

````scala
val fraction = new PartialFunction[Int, Int] {
  def apply(d: Int) = 42 / d
  def isDefinedAt(d: Int) = d != 0
}
````

Example: `PartialFunction`
==========================

````scala
val fraction = new PartialFunction[Int, Int] {
    def apply(d: Int) = 42 / d
    def isDefinedAt(d: Int) = d != 0
}
````

* `fraction(0)` => `java.lang.ArithmeticException: / by zero`
* `fraction.isDefinedAt(0)` => false
* `fraction.lift(0)` => None


Group exercise: Scala koan
==========================

* AboutPartialFunctions


`Either`
========

* Generalizes `Option`
* By convention, `Left` replaces None with a value, `Right` instead of `Some`
* Used for exception management


Exception management
====================

Dichotomy of exceptions:

* No exceptions
* Expected exceptions
* Unexpected exceptions


Compare vs C
============

* Distinguished error code - null pointer, -1, all of which can completely be used to cause subsequent crashes in code!
* `errno` global error variable (or usually macro to ensure thread locality)
* `setjmp`/`longjmp`
* Personal experience: complex C code seems to use all these approaches!


Compare vs Go
=============

* By convention, return the error code and the value
* Consistently check error codes at point of use of function
* Use `panic` and `recover` 


In practice
===========

````scala
def throwableToLeft[T](block: => T): Either[java.lang.Throwable, T] =
  try {
    Right(block)
  } catch {
    case ex => Left(ex)
  }
````

http://stackoverflow.com/questions/1193333/using-either-to-process-failures-in-scala-code


Usage
=====

````scala
val s = null
throwableToLeft { s.toUpperCase } match {
  case Right(s) => println(s)
  case Left(e) => e.printStackTrace
}
````

Manipulation of `Either`
========================

* Projection with `left`, `right`
* Similar to `lift` for `Option`


What does this remind you of?
=============================

... algebra!

* Just an abstract algebra
* Actually it's abstract algebra, specifically from monoids ;)
* Out of scope for this class... other than to discuss humorously


Formalization
=============

* Doesn't prescribe a specific interface
* Instead, we can describe
* Monads don't have to be restricted to specific interfaces, or a given interface
* Instead they describe a shape - we can always adapt
* However best to use common mechanisms


Essence of a monad
==================

* Construction - 
* Composition - chaining together monads
* Existence of an identity


Even more monads
================

* Sequence - "programmable semicolon"
* List
* Tree
* State
* IO
* Continuation
* etc



Fundamental concept of a monad
==============================

* Monads are structured computations
* Monads can be constructed (= unit, return)
* Possible to compose them (= bind, combine, chain, join)


Programmable semicolon
======================

Some sequence of code, possibly including side effects, encapsulated in a function:

````scala
def foo(bar: String) = {
  println(bar)
  bar.length
}
````

Example from http://www.codecommit.com/blog/ruby/monads-are-not-metaphors


Programmable semicolon
======================

Or put in a semicolon:

````scala
def foo(bar: String) = {
  println(bar); bar.length
}
````


Programmable semicolon
======================

Possibly wrap in some additional anonymous functions:

````scala
def foo(bar: String) = {
  ({ () => println(bar) })()
  ({ () => bar.length })()
}
````

(Yes, it's possible to write such ugly code)


Programmable semicolon
======================

Make explicit the connection between functions - the "semicolon":

````scala
def foo(bar: String) = {
  ({ () => println(bar) } andThen
   { () => bar.length })()
}
````


Programmable semicolon: what have we seen?
==========================================

Still not there yet:

* This example is not yet a monad
* But it exposes some things we would expect
* Structured computation - by encapsulating in anonymous functions
* Along with composition


Monadic expectations
====================

What do we expect of the combination process, mathematically speaking?

* ?
* Composition should be associative
* Composition should support an identity
* Anything else?


Fundamental operations
======================

* **return** - construct the monad
* **bind** - compose two monads together


`Option` implementation
=======================

Representative parts of the `Option` class in Scala:

````scala
sealed abstract class Option[+A] 
  extends Product with Serializable {

  def isEmpty: Boolean
  def isDefined: Boolean = !isEmpty
  def get: A
	
  def map[B](f: A => B): Option[B] =
    if (isEmpty) None else Some(f(this.get))

  def flatMap[B](f: A => Option[B]): Option[B] =
    if (isEmpty) None else f(this.get)
````

(omitting `final`, `@inline`, comments, etc)


Additional higher-order functions
=================================

Monads are not just about construction and composition; they can be
arbitrarily rich in the interfaces they support:

````scala
  def fold[B](ifEmpty: => B)(f: A => B): B =
    if (isEmpty) ifEmpty else f(this.get)

  def exists(p: A => Boolean): Boolean =
    !isEmpty && p(this.get)

  def forall(p: A => Boolean): Boolean =
    isEmpty || p(this.get)
  ....
````


`Some` implementation
=====================

Simply contain something:

````scala
final case class Some[+A](x: A) extends Option[A] {
  def isEmpty = false
  def get = x
}
````


`None` implementation
=====================

Or contain **nothing** at all:

````scala
case object None extends Option[Nothing] {
  def isEmpty = true
  def get = throw new 
    NoSuchElementException("None.get")
}
````

`Option` usage
==============

Example from earlier:

````scala
val name: Option[String] =
 request getParameter "name"
val upper = name map { _.trim } filter
 { _.length != 0 } map { _.toUpperCase }
println(upper getOrElse "")
````

`Option` usage
==============

Or to compose more generally:

````scala
val name: Option[String] =
 request getParameter "name"
val upper = name flatMap { _.trim } filter
 { _.length != 0 } flatMap { _.toUpperCase }
println(upper getOrElse "")
````


For expressions, re-expressed
=============================

In a for expression:

````scala
for (x <- expr1) yield expr2
````

is equivalent to

````scala
expr1.map(x => expr2)
````

From section 23.4 of *Programming in Scala 2ed*


generator and filter
====================

````scala
for (x <- expr1 if expr2) yield expr3
````
	
is equivalent to

````scala
for (x <- expr1 withFilter (x => expr2)) yield expr3
````

withFilter?
===========

* Equivalent to `filter`
* But guarantees proper ordering
* Why is this necessary?
* => to avoid building an intermediate result
* Does so by using an intermediate object to manage the filtering



From the docs
=============

From the definition of scala.collection.TraversableLike:

````scala
  /* ...
   *  Note: the difference between `c filter p` and `c withFilter p` is that
   *        the former creates a new collection, whereas the latter only
   *        restricts the domain of subsequent `map`, `flatMap`, `foreach`,
   *        and `withFilter` operations.
   * ...
   */
````

http://scala-programming-language.1934581.n4.nabble.com/Rethinking-filter-td2009215.html#a2009218




withFilter in `Option`
======================

````scala
  @inline final def withFilter(p: A => Boolean): 
     WithFilter = new WithFilter(p)

  class WithFilter(p: A => Boolean) {
    def map[B](f: A => B): Option[B] =
	  self filter p map f
    def flatMap[B](f: A => Option[B]): Option[B] =
	  self filter p flatMap f
    def foreach[U](f: A => U): Unit =
	  self filter p foreach f
    def withFilter(q: A => Boolean): WithFilter =
	  new WithFilter(x => p(x) && q(x))
  }
````


Using `withFilter`
==================

````scala
for (x <- expr1 if expr2) yield expr3
````
	
is equivalent to

````scala
for (x <- expr1 withFilter (x => expr2)) yield expr3
````

then by the `map` equivalent:

````scala
expr1 withFilter (x => expr2) map (x => expr3)
````

for expressions with two generators
===================================

This is where it gets interesting - we are seeing composition:

````scala
for (x <- expr1; y <- expr2; seq) yield expr3
````
	
where seq is an arbitrary sequence:

> * of generators, definitions, and filters
> * can be empty 


for expressions with two generators
===================================

Translate

````scala
for (x <- expr1; y <- expr2; seq) yield expr3
````

to

````scala
expr1.flatmap(x => for (y <- expr2; seq) yield expr3)
````

`flatMap`?
==========

Definition of `flatMap` in `Option`:

````scala
def flatMap[B](f: A => Option[B]): Option[B] =
  if (isEmpty) None else f(this.get)
````

* `flatMap` is just a monadic bind!
* Takes a `B`, returns back the wrapped `Option[B]` - right there it's in the signature!!!


Concrete example
================

Find all authors who have published at least two books 

````scala
for (b1 <- books;
     b2 <- books if b1 != b2;
     a1 <- b1.authors;
	 a2 <- b2.authors if a1 == a2)
  yield a1
````

From PiS2e, p490


Translated
==========

A bit dense, but this is the actual computation on the underlying collection:

````scala
books flatMap (b1 =>
  books withFilter (b2 => b1 != b2) 
  flatMap (b2 =>
    b1.authors flatMap (a1 =>
	  b2.authors withFilter
	    (a2 => a1 == a2)
	  map (a2 => a1))))
````


Query implications
==================

* Should remind you of SQL
* Query into a specific physical plan
* Actually can be translated into SQL!
* LINQ, part of C# uses this idea
	

`for` without `yield`
=====================

Can write for loops with side effects:

````scala
for (x <- expr1) body
````
	
translates to

````scala
expr1 foreach (x => body)
````


Group exercise
==============

The nested loops join is a general mechanism for implementing the join
seen in relational databases. Let's define it as follows: it combines two lists `xs`
and `ys` such that a tuple `(x, y)` is generated if the predicate
`p(x, y)` is true for each `x` in `xs` and each `y` in `ys`.

The following for-expression is one version of a nested-loops
join. Implicitly this for-expression takes advantage of monadic
composition on Lists, where `List(x)` implements *unit* and
`List.flatMap` implements *bind*:

````scala
def join[A,B](xs: List[A],
              ys: List[B],
              p: (A, B) => Boolean):List[(A,B)] = {
  for (x <- xs;
       y <- ys 
       if p(x, y)) yield (x, y)
}
````

Rewrite `join` in terms of `map`, `flatMap`, and `withFilter`. Apply
the translation scheme discussed in class.
