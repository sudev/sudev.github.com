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

