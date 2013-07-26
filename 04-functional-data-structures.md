Case classes
============

* Simple to define: `case class Point(x: Int, y: Int)` is a complete definition
* Cases classes automatically get `hashCode`, `equals`, and getters
* Instances are immutable
* No need to use `new` to construct, just use the name of the class as a constructor
* Can add methods as desired


Koans group exercise
====================

* AboutClasses
* AboutCaseClasses


Case classes, by example
========================

````scala
sealed abstract class Expr
case class Var(name: String) extends Expr
case class Number(num: Int) extends Expr
case class UnOp(op: String, arg: Expr) extends Expr
case class BinOp(op: String, left: Expr, right: Expr) extends Expr
````

Example from *Programming in Scala, 2nd Edition*


Recursive pattern matching
==========================

Case classes make it easy to pattern match:

````scala
def simplify(expr: Expr): Expr = expr match {
  case UnOp("-", UnOp("-", e)) =>
    simplify(e)
  case BinOp("+", e, Number(0)) =>
    simplify(e)
  case BinOp("*", e, Number(1)) =>
    simplify(e)
  case UnOp(op, e) =>
    UnOp(op, simplify(e))
  case BinOp(op, left, right) =>
    BinOp(op,
          simplify(left),
          simplify(right))
  case _ => expr
}
````

`SearchTree` problem
====================

A binary search tree is a binary tree with the following structure:

$n$ is any node in a binary search tree whose data
value is $d$, left child is $l$, and right child is $r$

that satisfies an ordering invariant:

>* all of the data values in the subtree rooted at $l$ must be $< d$
>* all of the data values in the subtree rooted at $r$ must be $\geq d$


Functional `SearchTree`
=======================

* We will use case classes for the implementation
* => no mutation of the case classes
* What is the equivalent for working with `List`?
* => copies, but with sharing, to create what appears to be mutation
* While minimizing copies (can I do better than what this problem directs? yes...)


Implementing `SearchTree`
=========================

Start with these class and object definitions:

````scala
sealed abstract class SearchTree
case object Empty extends SearchTree
case class Node(l: SearchTree, d: Int, r: SearchTree)
  extends SearchTree
````

Follows a problem written by my colleague Evan Chang


Recursively verify tree invariants
==================================

````scala

def repOk(t: SearchTree): Boolean = {
  def check(t: SearchTree, 
            min: Int, max: Int): Boolean = 
    t match {
      case Empty => true
      case Node(l, d, r) => 
        throw new UnsupportedOperationException
    }
  check(t, Int.MinValue, Int.MaxValue)
}
````

Insertions
==========

* Need to implement *functional* insertion
* Why?
* ... given our class definitions, not just because it's cool...
  
```scala
def insert(t: SearchTree, n: Int): SearchTree =
  throw new UnsupportedOperationException

def treeFromList(l: List[Int]): SearchTree =
  ((Empty: SearchTree) /: l)(insert)
````

Functional deletions
====================

If you have time...


````scala

def deleteMin(t: SearchTree): (SearchTree, Int) = {
  require(t != Empty)
  (t: @unchecked) match {
     case Node(Empty, d, r) => (r, d)
     case Node(l, d, r) =>
       val (l1, m) = deleteMin(l)
       throw new UnsupportedOperationException
  }
}
 
def delete(t: SearchTree, n: Int): SearchTree = 
  throw new UnsupportedOperationException
````

Implementing `sum`
==================

Start with the type signature

````scala
def sum(t: SearchTree): Int
````

Skeleton
========

````scala
def sum(t: SearchTree): Int = t match {
  case Empty => 0
  case Node(l, d, r) => 
    throw new UnsupportedOperationException
}
	
assert(sum(Empty) == 0)
assert(sum(Node(Empty, 42, Empty)) == 42)
assert(sum(Node(Node(Empty, 5, Empty), 42, Empty))
       == 47)
````

Solution for `sum`
==================

````scala
def sum(t: SearchTree): Int = t match {
  case Empty => 0
  case Node(l, d, r) => sum(l) + d + sum(r)
}
	
assert(sum(Empty) == 0)
assert(sum(Node(Empty, 42, Empty)) == 42)
assert(sum(Node(Node(Empty, 5, Empty), 42, Empty))
       == 47)
````

Question: can `sum` be rewritten so it's tail recursive?