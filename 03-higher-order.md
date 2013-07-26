% Tail recursion, higher-order functions
% Jim Baker

Recursive `factorial`, yet again
================================

Recursive implementation of `factorial` that we saw earlier:

````scala
def factorial(n: Int): Int = {
  require(n >= 0)
  n match {
    case 0 => 1
    case _ => n * factorial(n - 1)
  }
}
````

* What concerns do we have about recursive implementations?
* How we would rewrite this again to be imperative?
* Can the compiler just do that for us????!


Iterative version
=================

Maybe this is what we would like the compiler to do - the first implementation we tried:

````scala
def factorial(n: Int): Int = {
  require(n >= 0)
  var i = n
  var acc: Int = 1
  while (i > 0) {
    acc *= i
    i -= 1
  }
  return acc
}
````


Tail recursion
==============

* The fact that our implementation of factorial must do
more work after it is done calling itself tells us that
our implementation is NOT tail recursive.

* In order to be tail recursive we need an implementation 
of factorial in which all the work is done before it recurses.


Rewrite factorial to be tail recursive
=======================================

Solution:

````scala
def factorial(n: Int): Int = {
  require(n >= 0)  
  def loop(result: Int, n: Int) =
    if (n == 0) result
    else loop(result * n, n - 1)
  loop(1,n)
}
````

* Using `loop` is fairly conventional to state to your reader this is
  tail recursion; no meaning otherwise

* The last call is to itself - that's why it can loop without
  requiring a recursive call (and growing stack frames, etc)


`tailrec` annotation
====================

* Does not magically make your code recursive!
* But does warn if it is not AND you expected it is


Example
=======

````scala
import scala.annotation.tailrec

def factorial(n: Int): Int = {
  require(n >= 0)

  @tailrec
  def loop(result: Int, n: Int): Int =
    if (n == 0) result
    else loop(result * n, n - 1)
  loop(1,n)
}
````


Group question
==============

* Can `ack` be made tail recursive with the `@tailrec` annotation?
* Trick question: what does that mean, "made"?


S-99: Ninety-nine Scala problems
================================

* Adapted from an original list of Prolog problems
* Simple, sweet, good way to think through problems *functionally*, using higher-order approaches


http://aperiodic.net/phil/scala/s-99/


P01 - Find the last element of a list
=====================================

````scala
assert(last(List(1, 1, 2, 3, 5, 8)) == 8)
````

Solution
========

````scala
def last[T](xs: List[T]): T = xs match {
  case x :: Nil => x
  case x :: xs1 => last(xs1)
  case Nil => throw new NoSuchElementException
}
````


P02 - Find the last but one element of a list
=============================================

````scala
assert(penultimate(List(1, 1, 2, 3, 5, 8)) == 5)
````


Solution
========

````scala
def penultimate[T](xs: List[T]): T = xs match {
  case x :: _ :: Nil => x
  case x :: xs1 => penultimate(xs1)
  case Nil => throw new NoSuchElementException
}
````

Group exercises
===============

>* P03 Find the Kth element of a list

````scala
def nth[T](n: Int, xs: List[T]): T  //signature

assert(nth(2, List(1, 1, 2, 3, 5, 8)) == 2)
````

>* P04 Find the number of elements of a list

````scala
def length[T](xs: List[T]): Int    //signature

assert(length(List(1, 1, 2, 3, 5, 8)) == 6)
assert(length(Nil) == 0)
````


Solutions
=========

````scala
def nth[T](n: Int, xs: List[T]): T = xs match {
  case x :: xs1 if n == 0 => x
  case x :: xs1 => nth(n-1, xs1)
  case Nil => throw new NoSuchElementException
}
````

````scala
def length[T](xs: List[T]): Int = xs match {
    case Nil => 0
    case _ :: tail => 1 + length(tail)
}
````


Tail recursive version of `length`
==================================

Tail recursion
==============

Move calculation into an accumulator:

````scala
def length[T](xs: List[T]): Int = {
    @tailrec
    def lengthTR(n: Int, xs: List[T]): Int = xs match {
        case Nil => n
        case _ :: tail => lengthTR(n+1, tail)
    }
    lengthTR(0, xs)
}
````


P05 Reverse a list
==================

````scala
assert(reverse(List(1, 1, 2, 3, 5, 8)) ==
  List(8, 5, 3, 2, 1, 1))
````


Solution
========

Simply use `:::` which concatenates two lists together:

````scala
def reverse[T](xs: List[T]): List[T] = xs match {
  case Nil => Nil
  case x :: xs1 => reverse(xs1) ::: x :: Nil
}
````


(Bad) Solution
==============

Actually that's a terrible solution; why?

````scala
def reverse[T](xs: List[T]): List[T] = xs match {
  case Nil => Nil
  case x :: xs1 => reverse(xs1) ::: x :: Nil
}
````

`foldLeft` on `List` objects
============================

* A higher-order function: `def foldLeft[B](z: B)(f: (B, A) => B): B`
* Left associative
* Note that it takes two parameter lists - curried/partially applicative

````scala
def sum(xs: List[Int]): Int = {
  xs.foldLeft(0) (_ + _)
}
````


`/:` as synonym
===============

````scala
def sum(xs: List[Int]): Int = {
  (0 /: xs) (_ + _)
}
````


Solution with `foldLeft`
========================

````scala
def reverse[T](xs: List[T]): List[T] =
  xs.foldLeft(List[T]()) { (ys, y) => y :: ys }
````


Or this one
===========

````scala
def reverse[T](xs: List[T]): List[T] =
  (List[T]() /: xs) { (ys, y) => y :: ys }
````

Or this tail recursive version
==============================

````scala
def reverse[T](xs: List[T]): List[T] = {
  @tailrec
  def reverseTR(xs: List[T], curr: List[T]): List[T] =
    curr match {
      case Nil => xs
      case head :: tail => 
        reverseTR(head :: xs, tail)
    }
  reverseTR(Nil, xs)
}
````


P06 Find out whether a list is a palindrome
===========================================

````scala
assert(isPalindrome(List(1, 2, 3, 2, 1)))
````


Solution
========

?

map
===

`xs map f` - apply `f` to all elems in `xs`

````scala
def map[B](f: (A) => B): List[B]
````

Example
=======

````scala
def square(x: Int): Int = x * x
List(1,2,3,4,5) map square
````

`map` vs `for` expression
=========================

Equivalence:

````scala
for (x <- List(1,2,3,4,5)) yield square(x)
````

* We will see that this equivalence is actually very deep


Group exercise
==============

Complete the following koans:

* AboutForExpressions
* AboutHigherOrderFunctions


P08 Eliminate consecutive duplicates of list elements
=====================================================

````scala
assert(compress(List('a, 'a, 'a, 'a, 'b, 'c, 'c,
                     'a, 'a, 'd, 'e, 'e, 'e, 'e)) == 
       List('a, 'b, 'c, 'a, 'd, 'e))
assert(compress(List('a, 'b, 'c, 'c, 'a, 'a, 'd, 'e)) ==
       List('a, 'b, 'c, 'a, 'd, 'e))
````

`zip`
=====

Given `xs` and `ys`, return a list of tuples, paired up:

````scala
def zip[B](that: GenIterable[B]): List[(A, B)]
````

Solution
========

````scala
def compress[T](xs: List[T]): List[T] = {
  xs.head :: (for ((a, b) <- xs.zip(xs.tail)
                  if a != b)
                  yield b)
}
````


More on higher-order functions
==============================

* filtering lists - `filter`, `find`, `partition`, `dropWhile`, `takeWhile`, `span`
* predicates on a list - `forall`, `exists`


filter
======

xs `filter` p - returns the list of elems for which p is true


````scala
def odds(xs: List[Int]):List[Int] = {
  xs filter (_ % 2 == 1)
}
````


filter, for expression variant
==============================

````scala
def odds(xs: List[Int]):List[Int] = {
  for (x <-xs if x % 2 == 1) yield x
}
````


find
====

`xs find p` - first element in `xs` that satisfies `p`

````scala
def isOdd(x: Int): Boolean = x % 2 == 1
	
List(1, 2, 3, 4, 5) find isOdd
List(1, 2, 3, 4, 5) find (_ % 2 == 1)
````
	

partition
=========

`xs partition p` - returns tuple of two lists - (satisifes `p`, doesn't satisfy `p`)

````scala
List(1, 2, 3, 4, 5) partition isOdd
List(1, 2, 3, 4, 5) partition (_ % 2 == 1)
````
	

`takeWhile`
===========

`xs takeWhile p` - returns a list with the longest prefix satisfying `p`

````scala
List(1, 2, 3, 4, 5) takeWhile isOdd
List(1, 2, 3, 4, 5) takeWhile (_ < 3)
List(1, 2, 3, 4, 5) takeWhile (_ > 3)
````

`dropWhile`
===========

`xs dropWhile p` - returns the remaining list *after* the longest prefix satisfying `p`

````scala
List(1, 2, 3, 4, 5) dropWhile isOdd
List(1, 2, 3, 4, 5) dropWhile (_ < 3)
List(1, 2, 3, 4, 5) dropWhile (_ > 3)
````


`span`
======

`xs span p` is the same as `(xs takewhile p, xs dropwhile p)`

Returns the (longest prefix, remainder of list)


P09 Pack consecutive duplicates of list elements into sublists
==============================================================

````scala
assert(pack(List('a, 'a, 'a, 'a, 'b, 'c, 'c,
                 'a, 'a, 'd, 'e, 'e, 'e, 'e)) ==
       List(List('a, 'a, 'a, 'a),
            List('b),
            List('c, 'c),
            List('a, 'a),
	    List('d),
	    List('e, 'e, 'e, 'e)))
````

Solution
========

````scala
def pack[T](xs: List[T]): List[List[T]] = xs match {
  case Nil => List(Nil)
  case _ => {
    val (packed, next) = xs.span { _ == xs.head }
    next match {
      case Nil => List(packed)
      case _ => packed :: pack(next)
    }
  }
}
````


`exists`, `forall`
==================

`xs exists p` is true if any elements in the list `xs` satisfy `p`

`xs forall p` is true if all elements in the list `xs` satisfy `p`

````scala
def hasZeroRow(m: List[List[Int]]) = {
  m exists (row => row forall (x == 0))
}
````


`exists` using `foldLeft`
=========================

````scala
def exists[T](xs: List[T],
              p: T => Boolean): Boolean = {
  xs.foldLeft(false)(_ || p(_))
}
````
	
	
Short group exercise: `forall` using `foldLeft`
===============================================
	
````scala
def forall[T](xs: List[T],
              p: T => Boolean): Boolean = {
  throw new UnsupportedOperationException
}
````


Solution: `forall` using `foldLeft`
===================================

````scala
def forall[T](xs: List[T],
              p: T => Boolean): Boolean = {
  xs.foldLeft(true)(_ && p(_))
}
````


Function values, recap
======================

````scala
def square(x: Int):Int = x * x
val square: Int => Int = (x: Int) => x * x
var f: Int => Int = (x: Int) => x * x
f = (x: Int) => 2 * x
````


Recursion with function values?
===============================

How can you recurse without a name?

````scala
def factorial(n: Int): Int = {
   require(n>=0)
   n match {
     case 0 => 1
     case _ => n * factorial(n - 1) 
   }
}
````

This function can call itself because it knows what to call... right?


Fixed points
============

* Definition: a fixed point of a function $f$ is an input that is equal to its output
* Equivalently: $x$ is a fixed point of the function $f$ if $x = f(x)$
* Functions can no fixed points, or they may have many
* Think of a quadratic equation

Follows this presentationm, albeit for JavaScript: http://matt.might.net/articles/implementation-of-recursive-fixed-point-y-combinator-in-javascript-for-memoization/


Demo: Wolfram Alpha
===================

So if we have $x = f(x)$, what about

* $x = x^2$ ?
* $x = x^2 - 2$ ?
* $x = x^2 + 5$ ?
* $x = x^3 - 2x^2 + 10$ ?

Just try out at http://www.wolframalpha.com/


Back to grade school algebra
============================

* What if there was a general way to solve for `x`?
* We know this. Example: rewrite the equation as $ax^2+bx+c=0$
* Then $x=\frac{-b\pm\sqrt{b^2-4ac\ }}{2a}$


Fixed points on functions!
==========================

What if we could do the same thing with

$f = F(f)$ ?

* Remember our demo: we have solved for $x$
* Could we also solve for $f$?
* If so, we could eliminate recursion from a recursive function
* `F` would be a fixed point
* No recursion, no iteration, instead we would only have explicit calls
* Actually can be useful through *memoization*


Solution: fixed point combinator in Scala
=========================================

One possibility:

````scala
def fix[A,B](f: (A=>B)=>(A=>B)): A=>B = f(fix(f))(_)
````

* There are other combinators, that don't rely on recursion at all in the combinator => Y combinator
* Hard to do with Scala's type system; much easier for dynamically typed languages



Fixed point with factorial function
===================================

````scala
def fix[A,B](f: (A=>B)=>(A=>B)): A=>B = f(fix(f))(_)

val factorial = fix[Int,Int](f => n => {
  require(n>=0)
  n match { 
    case 0 => 1
    case _ => f(n-1) * n
}})
````
