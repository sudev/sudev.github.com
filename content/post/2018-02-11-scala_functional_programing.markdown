---
categories:
- notes
comments: true
date: 2018-02-11T00:00:00Z
tags:
- functional programming
- Scala
title: Notes on Functional Programming and Scala
draft: true
---


## Lists

* Implement a method `xs.last` to find the last element of a list. 

``` scala 
def last[T](xs: List[T]): T = xs match { 
	case List() => thriw new Error ("Last of empty list")
	case List(x) => x
	case y :: ys => last(ys)
}
```


* Implement a method `List.init` which returns all elements of the list except the last element.

``` scala 
def init[T](xs: List[T]): List[T] = xs match { 
	case List() => throw new Error("Init of empty list")
	case List(x) => List() // Empty List
	case y :: ys => y :: init(ys)
}
```

* Implement a method `List.concat` which concatenates two given lists. 

``` scala 
def concat[T](xs: List[T], ys: List[T]): List[T] = xs match {
	case List() => ys
	case z :: zs => z :: concat(zs, ys)
}
```

* List.reverse


``` scala 
def reverse[T](xs: List[T]): List[T] = xs match { 
	case List() => xs
	case y :: ys => reverse(ys) ::: List(y)
}
```

The above implementation is of the complexity `O(n2)`.

* Remove nth element of a list

``` scala 
def removeAt[T](n: Int, xs: List[T]): List[T] = { 
	def loop(c: Int, inputList: List[T], newList: List[T]): List[T] = { 
		if (c == n) {
			newList ::: inputList.tail 
		} else {
			loop(c-1, inputList.tail, newList ::: List(inputList.head))
		}
	}
	loop(0, xs, List[T]())
}
```

Implementation using take and drop
``` scala 
def removeAt[T](n: Int, xs: List[T]): List[T] = 
	(xs take n) ::: (xs drop n+1)
```

* Implement a function to flatten a list structure.

`List(1, List(3,4), List(5,List(6)))` => `List(1,3,4,5,6)`

``` scala 
def flatten(xs: List[Any]): List[Any] = xs match {
  case Nil => Nil
  case (head: List[_]) :: tail => flatten(head) ++ flatten(tail)
  case head :: tail => head :: flatten(tail)
}

// Another way to pattern match
def flatten2(xs: List[Any]): List[Any] = xs match {
  case List() => List()
  case (y :: ys) :: yss => flatten(y :: ys) ::: flatten(yss)
  case y :: ys => y :: flatten(ys)
}
```


## Pairs and tuples

* Merge Sort 

``` scala 
def merge(left: List[Int], right: List[Int]): List[Int] = left match {
  case Nil => right
  case x :: xs1 => right match {
    case Nil => left
    case y :: ys1 => if (x > y) (y :: merge(left, ys1)) else (x :: merge(xs1, right))
  }
}

def msort(xs: List[Int]): List[Int] = {
  val n = xs.length / 2
  if (n == 0) xs
  else {
    val (left, right) = xs splitAt n
    // Divide and conquer
    merge(msort(left), msort(right))
  }
}

msort(List(1, 25, 123, 3, 5))
// List(1,3,5,25,123)
```

### Tuples in Scala 

``` scala 
case class Tuple2[T1, T2](_1: +T1, _2: +T2) {
  override def toString = "(" + _1 + "," + _2 + ")"
}
```

Tuples in scala have similar implementation to the one given above, this has two elements and each element can be accessed using the pattern `t._n` where n is the `nth` element. 

``` scala 
// Using pattern matching
val (one, two) = pair
// Using fields
val one = pair._1
val two = pair._2
```

We can use the pattern matching for the `nested match case` that we wrote for mergeSort earlier. So if we have to rewrite the merge sort with pair pattern matching we can do so as given below. 

``` scala
// Merge using pair pattern matching 
def merge(left: List[Int], right: List[Int]): List[Int] = (left, right) match {
  case (Nil, right) => right
  case (left, Nil) => left
  case (x :: xs, y :: ys) if (x > y) => (y :: merge(left, ys))
  case (x :: xs, y :: ys) => (x :: merge(xs, right))
}
```


Insertion sort in scala. 

``` scala
// Insert function. compares values and insert a data point to it's right place. 
def insert(x: Int, xs: List[Int]): List[Int] =
  xs match {
    case Nil => List(x)
    case y :: ys if (y >= x) => x :: xs
    case y :: ys => y :: insert(x, ys)
  }

// Runner for insertion sort.
def isort(arr: List[Int]): List[Int] =
  arr match {
    case List() => List()
    case x :: xs => insert(x, isort(xs))
  }
```


### Implicit Parameters


How can we parameterize the Merge Sort we wrote before so that it works for arbituary types? 

``` scala
  def merge[T](left: List[T], right: List[T]): List[T] = (left, right) match {
    case (Nil, right) => right
    case (left, Nil) => left
    // We will have an error here as the comparison operators are not the same 
    // for arbitrary types(Int/String/Char).
    case (x :: xs, y :: ys) if (x > y) => (x :: merge(xs, right))
    case (x :: xs, y :: ys) => (y :: merge(left, ys))
  }
```

If we simply parameterize the `merge` function like in the above snippet, the problem is that the comparison operators(`>` and `<`) are not the same for Int, String or any other type.

``` scala
def msort[T](xs: List[T])(comp: (T, T) => Boolean): List[T] = {
  val n = xs.length / 2
  if (n == 0) xs
  else {
    def merge(left: List[T], right: List[T]): List[T] = (left, right) match {
      case (List(), right) => right
      case (left, List()) => left
      case (x :: xs, y :: ys) if comp(x, y) => (x :: merge(xs, right))
      case (x :: xs, y :: ys) => (y :: merge(left, ys))
    }

    val (left, right) = xs splitAt n
    merge(msort(left)(comp), msort(right)(comp))
  }
}

def main(args: Array[String]): Unit = {
  // Integers
  def compInt(x: Int, y: Int) = (x > y)
  val nums = List(1,2,1,231,125,12)
  println(msort(nums)(compInt))

  //Strings
  val fruits = List("apple", "pineapple", "banana")
  def compString(x: String, y: String) = (x.compareTo(y) < 0)
  println(msort(fruits)(compString))
}
```

The above code parameterises the input and takes another function `comp` which comes up with a comparison function for arbitrary types like the `compString` and `compInt`.


``` scala 
import math.Ordering

def msort[T](xs: List[T])(ord: Ordering[T]): List[T] = {
  val n = xs.length / 2
  if (n == 0) xs
  else {
    def merge(left: List[T], right: List[T]): List[T] = (left, right) match {
      case (List(), right) => right
      case (left, List()) => left
      case (x :: xs, y :: ys) if ord.lt(x, y) => (x :: merge(xs, right))
      case (x :: xs, y :: ys) => (y :: merge(left, ys))
    }

    val (left, right) = xs splitAt n
    merge(msort(left)(ord), msort(right)(ord))
  }
}

def main(args: Array[String]): Unit = {
  // Ints
  def compInt(x: Int, y: Int) = (x > y)
  val nums = List(1, 2, 1, 231, 125, 12)
  println(msort(nums)(Ordering[Int]))
  // Strings
  val fruits = List("apple", "pineapple", "banana")
  def compString(x: String, y: String) = (x.compareTo(y) < 0)
  println(msort(fruits)(Ordering[String]))
}
```
We rewrite the entire code with standard `Ordering` library, ordering contains comparisons for most of the types. But still we have to mention (pass the comparison function) during each function call, can we get rid of this? 

This is where scala `implicit` helps. As shown in the following code scala implicit will help in removing the redundant calls. The following code shows sample usuage of implicit. 

``` scala
def msort[T](xs: List[T])(implicit ord: Ordering[T]): List[T] = {
  val n = xs.length / 2
  if (n == 0) xs
  else {
    def merge(left: List[T], right: List[T]): List[T] = (left, right) match {
      case (List(), right) => right
      case (left, List()) => left
      case (x :: xs, y :: ys) if ord.lt(x, y) => (x :: merge(xs, right))
      case (x :: xs, y :: ys) => (y :: merge(left, ys))
    }

    val (left, right) = xs splitAt n
    merge(msort(left), msort(right))
  }
}


def main(args: Array[String]): Unit = {
  // Ints
  def compInt(x: Int, y: Int) = (x > y)
  val nums = List(1, 2, 1, 231, 125, 12)
  println(msort(nums))
  // Strings
  val fruits = List("apple", "pineapple", "banana")
  def compString(x: String, y: String) = (x.compareTo(y) < 0)
  println(msort(fruits))
}
```

How does implicit work?

When you write a implicit parameter and you dont pass the parameter, compiler will figure out the parameter with right type. 


Rules for Implicit...

- is marked implicit
- has a type compatible with `T`
- is visible at the point of the function call, or is defined in a companion object associated with function/variable. 

### Higher Order functions on Lists
Implement a function `map` on List.

``` scala 
abstract class List[T] { 
  def map[U](f: T => U): List[U] = this match {
    case Nil => this
    case x :: xs => f(x) :: xs.map(f)
  }
} 
```

Implement a function `filter` on List.

``` scala 
abstract class List[T] { 
  def filter(f: T => Boolean): List[T] = this match {
    case Nil => this
    case x :: xs => if (p(x)) x :: xs.filter(p) else xs.filter(f)
  }
} 
```

A function pack which will pack all of the consecutive elements together into a List. (Check the example given below)
``` scala 
val testList = List("a", "a", "a", "a", "b", "b", "c")
pack(testList) 
// List(List("a", "a", "a", "a"), List("b", "b"), List("c"))
```

``` scala
def pack[T](xs: List[T]): List[List[T]] = xs match {
  case Nil => Nil
  case x :: xs1 => {
    // Get the first pack and remaining as another list
    val (first, remaining) = xs span (y => y == x)
    // First element and recursive pack call on other elements.
    first :: pack(xs)
  }
}
```


### Reduction of List

* ReduceLeft 

``` scala
List(x1, x2, ...xn) reduceLeft op = (... ( x1 op x2) op ..) op xn
```

Sum, product usinf reduceLeft. 

``` scala 
def sum(xs: List[Int]) = (0 :: xs) reduceLeft (_ + _)
def product(xs: List[Int]) = (1 :: xs) reduceLeft (_ * _)
```

* FoldLeft 

Requires a accumulator `z`, the accumulator will be accumulated according to the `op`. In case of a empty list the accumulator is returned as the result.

``` scala
(List(x1,...,xn) foldLeft z)(op) = ( ... (z op x1) op ... ) op xn
```


``` scala 
abstract class List[T] { ...
  def reduceLeft(op: (T, T) => T): T = this match  { 
    // Error for empty lists.
    case Nil => throw new Error("Nil.reduceLeft")
    case x :: xs => (xs foldLeft op(x, z))(op))
  }

  def foldLeft[U](z: U)(op: (U, T) => U): U = this match { 
    // z is returned as value for empty lists.
    case Nil => z
    case x :: xs => (xs foldLeft op(x, z))(op)
  }
}
```

FoldRight and reduceRight will compute from the right.

``` scala
abstract class List[T] { ...
  def reduceRight(op: (T, T) => T): T = this match  { 
    // Error for empty lists.
    case Nil => throw new Error("Nil.reduceLeft")
    case x :: Nil => x
    case x :: xs => op(x, xs.reduceRight(op))
  }

  def foldRight[U](z: U)(op: (U, T) => U): U = this match { 
    // z is returned as value for empty lists.
    case Nil => z
    case x :: xs => (xs foldLeft op(x, z))(op)
  }
}
```

Implement a function mapFun using foldRight

``` scala 
def mapFun[T, U](xs: List[T], f: T => U): List[U] =
  (xs foldRight List[U]())((a: T, b: List[U]) => List(f(a)) ::: b)
```

Implement Length using foldRight. 

``` scala 
def lengthFun[T](xs: List[T]): Int = (xs foldRight 0)((_, len: Int) => len + 1)
```

## Scala Collections

### Map

`Q1` Given a positive integer n, find all pairs of positive integerss i and j, with `1 <= j < i < n` such that `i + j` is prime.

In a imperative programming languages we would use a nested loops to get all of the combinations of the (i, j). The following is the way in which you might want to approach this in a functional language. 

``` scala 
val n = 7
val vv = (1 until n) map (i =>
    (1 until i) map (j => (i, j)))
vv
```

Above code will result in the following data structure.

```scala
Vector(Vector(), 
       Vector((2,1)), 
       Vector((3,1), (3,2)), 
       Vector((4,1), (4,2), (4,3)), 
       Vector((5,1), (5,2), (5,3), (5,4)), 
       Vector((6,1), (6,2), (6,3), (6,4), (6,5))
      )
```

If you notice here the object returned is a vector of vectors. But, why? 

IndexedSeq is the class which is superset for both Range and Vectors. And the combination of both Range and Vector results in it's superset class type. 

Now to flatten the vectors, use `flatten`. 

```scala
vv.flatten
// Vector((2,1), (3,1), (3,2), (4,1), (4,2), (4,3), (5,1), (5,2), (5,3), (5,4), (6,1), (6,2), (6,3), (6,4), (6,5))
```

### flatMap

flatMap can be considered as the operation simillar to `xs flatMap f = (xs map f).flatten`. We could have produced the above seq using flatMap. 

``` scala
val vv2 = (1 until n) flatMap (i =>
    (1 until i) map (j => (i, j)))
```

To answer the `Q1` we have to check if the sum of these pair makes prime number. 

```scala
def isPrime(n: Int) = (2 until n) forall (n % _ != 0)
vv2.filter(x => isPrime(x._2 + x._1))
// Vector((2,1), (3,2), (4,1), (4,3), (5,2), (6,1), (6,5))
```

### For expression 

Assume 

``` scala
case class Person(name: String, age: Int)
```

To obtain the name of persons over 20 years old, you can write: 

``` scala 
for (p <- persons if p.age > 20) yield p.name 
```

The same can be achieved using filter/map. 

``` scala
persons filter (p => p.age > 20) map (p => p.name)
```

A for expression is of the following form

``` scala
for ( s ) yield e
// Or curly braces,  
for { s } yield e
```

Where `s` is sequence of `generators` and `filters` and `e` is an expression whose value is returned by an iteration. 

``` scala
for {
    i <- 1 until n
    j <- 1 until i
    if isPrime(i + j)
  } yield (i, j)
// Vector((2,1), (3,2), (4,1), (4,3), (5,2), (6,1), (6,5))
```

* If there are several generators in the sequence, the last generators vary faster than the first.  ???? 

Write a version of scalarProduct that make use of a for expression.  

``` scala
def scalarProduct(xs: List[Double], ys: List[Double]): Double =
    (for {
      (x, y) <- (xs zip ys)
    } yield x * y).sum
```

## Sets

Difference b/w `sets` and `seq`.

1. Sets are **unordered**
2. Sets have no dupilicates 
3. The fundamental operation on sets is **contains**

**N-Queens**

Problem description  -> [N Queens Problem][nqp]

``` scala

object nqueens {

  def isSafe(col: Int, ints: List[Int]): Boolean = {
    val row = ints.length
    val queensWithRow = (row - 1 to 0 by -1) zip ints
    queensWithRow forall {
      case (r, c) => col != c && math.abs(col - c) != row - r
    }
  }


  def queens(n: Int): Set[List[Int]] = {
    def placeQueens(k: Int): Set[List[Int]] = {
      // Base case
      if (k == 0) Set(List())
      else {
        val a = for {
          // Recursive calls
          queens <- placeQueens(k - 1)
          // Generate a possible for all columns.
          col <- 0 until n
          if isSafe(col, queens)
        } yield col :: queens
        // I couldn't understand the loops first uncomment to see intermediate results.
        println("k = " + k.toString + "\t" + a.toString)
        a
      }
    }
    placeQueens(n)
  }

  def show(queens: List[Int]) = {
    val lines =
      for (col <- queens.reverse)
        yield Vector.fill(queens.length)(" * |").updated(col, " Q |").mkString + "\n" + (0 until queens.length).map(_ => "----").mkString
    "\n\n" + (lines mkString "\n")
  }

  def main(args: Array[String]): Unit = {
    println(queens(4) map show)
  }

}
```

Results from the above code. 

``` scala
// println(queens(4) map show)
// These are the Set outputs for recursion calls
k = 0	Set(List())
k = 1	Set(List(0), List(1), List(2), List(3))
k = 2	Set(List(2, 0), List(0, 2), List(3, 1), List(1, 3), List(0, 3), List(3, 0))
k = 3	Set(List(3, 0, 2), List(0, 3, 1), List(2, 0, 3), List(1, 3, 0))
k = 4	Set(List(1, 3, 0, 2), List(2, 0, 3, 1))


Set(

 * | * | Q | * |
----------------
 Q | * | * | * |
----------------
 * | * | * | Q |
----------------
 * | Q | * | * |
----------------, 

 * | Q | * | * |
----------------
 * | * | * | Q |
----------------
 Q | * | * | * |
----------------
 * | * | Q | * |
----------------)
```



[nqp]: https://en.wikipedia.org/wiki/Eight_queens_puzzle

## Maps

```scala
val romanNumeral = Map("I" -> 1, "II" -> 2)
// Lookups
romanNumeral("II")
// Error is thrown 
romanNumeral("V")
```

Some, None 

``` scala 
romanNumeral get "V"
// Option[Int] = None
romanNumeral get "I"
// Option[Int] = Some(1)
```

## Option, Some

The Option type is defined as:

``` scala
trait Option[+A]

case class Some[+A](value: A) extends Option[A]

object None extends Option[Nothing]
```

Decomposing Option 

Since options are defined as case classes, they can be decomposed using pattern matching: (*why only case classes???*)

```Scala
def getNumeralValue(rnumeral: String) = romanNumeral.get(rnumeral) match {
  case Some(rval) => rval
  case None => "Notdefined"
}
getNumeralValue("I")
// Any = 1
getNumeralValue("V")
// Any = Notdefined
```

## More Map

Define a class polynomial using maps, Map[exponent, coefficient].

``` scala
class Poly(val terms: Map[Int, Double]) {
  // Operation to add two polynomials
  def +(other: Poly) = new Poly(terms ++ other.terms map adjust)

  // adjust function to add similar exponents
  def adjust(term: (Int, Double)): (Int, Double) = {
    val (exp, coe) = term
    terms get exp match {
      case Some(coef1) => exp -> (coe + coef1)
      case None => exp -> coe
    }
  }

  // To String
  override def toString: String = (for ((exp, coe) <- terms.toList.sorted.reverse) yield coe + "x^" + exp) mkString " + "
}
```

### Default Values in Maps

``` scala
val rnWithDefaults = romanNumeral withDefaultValue "Notdefined"
rnWithDefaults("V")
// Any = Notdefined
```

`withDefaultValue` sets a default value for missing keys in a map. 

Now we can use this feature to rewrite the Poly class by using a deafult value and getting rid of the case class. 

``` scala
class Poly(val terms0: Map[Int, Double]) {
  // Defining the default value
  val terms = terms0 withDefaultValue 0.0

  def +(other: Poly) = new Poly(terms ++ other.terms map adjust)

  def adjust(term: (Int, Double)): (Int, Double) = {
    val (exp, coe) = term
    // We don't need match statement now we can add
    // coe and default value will be picked up in case of nokey
    exp -> (coe + terms(exp))
  }
  
}
```



## Variable arguments

Say we want to avoid passing Map[Int, Double] as parameter to the Poly Function and want to pass something of the sorts as shown below.

```Scala
Poly(1 -> 2.0, 3 -> 4.0, 5 -> 6.2)
// Instead of Poly(Map())
```

``` scala
class Poly(val terms0: Map[Int, Double]) {
    // We are overloading the constructor here. 
    // But we can also do 
    // Poly(bindings: (Int, Double)*) 
  def this(bindings: (Int, Double)*) = this(bindings.toMap)
.
.
}
```

## Try[] - Error Handling

``` scala
case class Customer(age: Int)
class Cigarettes
case class UnderAgeException(message: String) extends Exception(message)
def buyCigarettes(customer: Customer): Cigarettes =
  if (customer.age < 16)
    throw UnderAgeException(s"Customer must be older than 16 but was ${customer.age}")
  else new Cigarettes
```

``` scala 
val youngCustomer = Customer(15)
try {
  buyCigarettes(youngCustomer)
  "Yo, here are your cancer sticks! Happy smokin'!"
} catch {
    case UnderAgeException(msg) => msg
}
```

Above code is how you would have a exception raised and hanled in Java/Ruby like code. This is definetly ugly. 



Scala way => **Try**

The semantics of `Try` are best explained by comparing them to those of the `Option`. 

Where `Option[A]` is a container for a value of type `A` that may be present or not, `Try[A]` represents a computation that may result in a value of type `A`, if it is successful, or in some `Throwable` if something has gone wrong. Instances of such a container type for possible errors can easily be passed around between concurrently executing parts of your application.

There are two different types of `Try`: If an instance of `Try[A]` represents a successful computation, it is an instance of `Success[A]`, simply wrapping a value of type `A`. If, on the other hand, it represents a computation in which an error has occurred, it is an instance of `Failure[A]`, wrapping a `Throwable`, i.e. an exception or other kind of error.



If we know that a computation may result in an error, we can simply use `Try[A]` as the return type of our function. This makes the possibility explicit and forces clients of our function to deal with the possibility of an error in some way.

For example, let’s assume we want to write a simple web page fetcher. The user will be able to enter the URL of the web page they want to fetch. One part of our application will be a function that parses the entered URL and creates a `java.net.URL` from it:

``` scala 
import scala.util.Try
import java.net.URL
def parseURL(url: String): Try[URL] = Try(new URL(url))
```

As you can see, we return a value of type `Try[URL]`. If the given `url` is syntactically correct, this will be a `Success[URL]`. If the `URL` constructor throws a `MalformedURLException`, however, it will be a `Failure[URL]`.

To achieve this, we are using the `apply` factory method on the `Try` companion object. This method expects a by-name parameter of type `A` (here, `URL`). For our example, this means that the `new URL(url)` is executed inside the `apply` method of the `Try`object. Inside that method, non-fatal exceptions are caught, returning a `Failure`containing the respective exception.

Hence, `parseURL("http://danielwestheide.com")` will result in a `Success[URL]`containing the created URL, whereas `parseURL("garbage")` will result in a `Failure[URL]` containing a `MalformedURLException`.





**To be Completed**



## Putting all pieces together

So here's the task. 

You know that phone keys have mnemonics assigned to them. If you look at your smart phone or another phone then you find that '2' gets associated with "ABC", '3' with "DEF", '4' with "GHI" and so on. Assume you're given a dictionary, which is a list of words that we call words for simplicity. 

What we want to do is design a method translate, such that translate(phoneNumber) would produce all phrases of words that can serve as mnemonics for the phone number. 

So, here is an example, the phone number that is given by the digit string, `7225427386` should have  the mnemonic `Scala is fun` as one element of the set of solution phrases. Why? Because the digit '7' has as one of he letters associated with it the S, 2 has both C and A, so that gives SCA. 

``` scala

import java.io.{File, PrintWriter}

import scala.io.Source
import scala.util.{Failure, Success, Try}

object Collectionss {
  /* read a file of words */
  implicit val cacheLoc = "/tmp/words.txt"
    
  // I'm still learning the best way to do TRY/CATCH in functional languages, 
  // please ignore teh function writeThroughCache, it might not be the efficient way.
  def writeThroughCache(implicit cacheLoc: String) = {
    val words = Source.fromURL("http://www.cis.syr.edu/courses/cis351/HW/Hw08/linuxwords.txt")
    val writer = new PrintWriter(new File(cacheLoc))
    writer.write(words.mkString)
    writer.close()
    Source.fromFile(cacheLoc)
  }

  val in = Try(Source.fromFile(cacheLoc)) match {
    case Failure(x) => writeThroughCache
    case Success(y) => y
  }

  /* create a list and filter all words where *all* their characters are not letters (like dashes) */
  val words = in.getLines.toList filter (word => word forall (chr => chr.isLetter))

  val nmem: Map[Char, String] = Map('2' -> "ABC", '3' -> "DEF", '4' -> "GHI", '5' -> "JKL", '6' -> "MNO", '7' -> "PQRS", '8' -> "TUV", '9' -> "WXYZ")

  val charCode: Map[Char, Char] = (for ((digit, str) <- nmem; c <- str) yield c -> digit)
  // My Version
  // val charCode: Map[Char, Char] = nmem.flatMap(x => x._2.map(y => (y, x._1)))

  def wordCode(str: String): String = str.toUpperCase map charCode


  val wordsForNum: Map[String, Seq[String]] = words groupBy wordCode withDefaultValue Seq()

  def encode(number: String): Set[List[String]] = {
    if (number.isEmpty) Set(List())
    else {
      {
        for {
          split <- 1 to number.length
          word <- wordsForNum(number take split)
          rest <- wordsForNum(number drop split)
        } yield word :: rest :: Nil
      }.toSet
    }
  }

  def translate(number: String): Set[String] = {
    encode(number).map(x => x mkString " ")
  }
  def main(args: Array[String]): Unit = {
    println("")
    println("")
    //print(wordsForNum("5282"))
    println(encode("76533567"))
    println(translate("76533567"))
  }
}
```