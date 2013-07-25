Case classes
============

FIXME

Koans group exercise
====================

* AboutClasses
* AboutCaseClasses


Expression trees in Scala
=========================

FIXME


`SearchTree` problem
====================

FIXME

`SearchTree`
============

Start with this class:

````scala
sealed abstract class SearchTree
case object Empty extends SearchTree
case class Node(l: SearchTree, d: Int, r: SearchTree) extends SearchTree
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

Final notes
===========

* Start with your tests
* Trust me!
