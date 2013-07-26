% Testing your Scala project
% Jim Baker


Overview
========

* Scala koans
* `sbt` tool
* Scala testing with ScalaTest


Koans
=====

* Good intro to build process with sbt
* Iterative process of write test, see it fail, modify code, see it (eventually) pass
* Train your fingers and eyes for working with Scala


Get the koans
=============

For now, let's use my workaround fork: https://bitbucket.org/jimbaker/scalakoansexercises

(forked from https://bitbucket.org/dmarsh/scalakoansexercises)

~~~~
hg clone ssh://hg@bitbucket.org/jimbaker/scalakoansexercises
~~~~


`sbt`
=====

* Describes dependencies, especially versions in `build.sbt`, process with `sbt`
* Why versions? -> Scala does introduce backwards-breaking changes
* `build.sbt` file can be very simple
* Complex `build.sbt` files are probably not a good idea... use some sort of scripting tool... :)


Example
=======

~~~~
name := "My awesome project"

version := "1.0"

scalaVersion := "2.10.2"

libraryDependencies ++= Seq(
	"org.scalatest" %% "scalatest" % "1.9.1" % "test" withSources() withJavadoc()
)
~~~~

NB: make sure you separate each setting with a blank line


Running the koans
=================

Build the koans:

~~~~
> test:compile
~~~~

Then run all the koans through the iterative process: 

~~~~
> ~test-only org.functionalkoans.forscala.Koans
~~~~


More on running the koans
=========================

Or

> namaste

Or, you can run specific koans (e.g., AboutAsserts) with:

> ~test-only org.functionalkoans.forscala.AboutAsserts

From http://www.scalakoans.org/installation


Demo
====

So let's try that out in sbt:

~~~~
> ~test-only org.functionalkoans.forscala.AboutAsserts
~~~~


Group exercise
==============

Complete the following koans:

* AboutValAndVar
* AboutLiteralNumbers
* AboutLiteralStrings
* About Tuples
* AboutLists
* AboutPatternMatching
* AboutPreconditions


ScalaTest
=========

* Just used this package implicity with the Scala koans!
* Just like unit testing packages for other languages
* Only the beginning of what's possible for testing - Scala is good for creating internal DSLs


Some basics
===========

* `sbt test` knows how to find and run your tests. Use that.
* `FlatSpec` is a good place to start for writing test functions
* `x should be(...)`
* `x should equal(...)` or `x === ...`
* `intercept[T]` to ensure an exception of type `T` is thrown


Group exercise
==============

* Create a new project with a simple sbt (use the Koans sbt as a start)
* Implement Fibonacci, factorial, Ackermann... your choice
* But first, write the tests for it
* More docs here: http://www.scalatest.org/user_guide


Project layout
==============

~~~~
+---README.md
+---build.sbt
+---src
    +---main
    |---+---scala
    |       +---ProblemSet.scala
    +---test
        |---scala
            |---ProblemSetSpec.scala
~~~~


Testing with `FlatSpec`
=======================

````scala
import org.scalatest.FlatSpec
import scala.collection.mutable.Stack

class StackSpec extends FlatSpec {

  "A Stack" should "pop values in last-in-first-out order" in {
    val stack = new Stack[Int]
    stack.push(1)
    stack.push(2)
    assert(stack.pop() === 2)
    assert(stack.pop() === 1)
  }

  it should "throw NoSuchElementException if an empty stack is popped" in {
    val emptyStack = new Stack[String]
    intercept[NoSuchElementException] {
      emptyStack.pop()
    }
  }
}
````
