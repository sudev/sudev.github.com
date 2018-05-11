---
categories:
- notes
comments: true
date: 2018-03-05T00:00:00Z
tags:
- functional programming
- Scala
- Spark
title: Notes on Spark Scala
draft: true
---





### Reduction Operations

walk through a collection and combine neighbouring elements of the collection together to produce a single combined result. 

Example 

``` scala
case class Taco(kind: String, price: Double)
val tacoOrder = List(Taco("Carnitas", 10), Taco("Corn", 20), Taco("Barcoa", 30))
val cost = tacoOrder.foldLeft(0.0)((sum, taco) => sum + taco.price)
println(cost)
// 60.00
```

* **foldLeft is not parallelizable**.  Applies abinary operator to start value and all elements of this collection or iterator, going left to right. 

![Spark2foldLeftTypeError](/images/sparkScala/Spark2foldLeftTypeError.png)

As you can see here, when foldleft is applied in parallel to the List, we end up with type errors during the intermediate stages. 

**Fold** allows us to parallelize things, but it **restricts us to always returning the same type**. With the same type for the output fold can come up with parallelizable reduce trees. 

``` scala 
// input and o/p of the same type. 
def fold(z: A)(f: (A, A) => A): A
```

But this was not the case with foldLeft and foldRight. 

``` scala
def foldLeft[B](z: B)(op: (B, A) ⇒ B): B
def foldRight[B](z: B)(op: (A, B) ⇒ B): B
```

* Aggregate 

``` scala
aggregate[B](z: => B)(seqop: (B,A) => A, combop: (B,B) => B): B
```

Now we have three parameters in two parameter lists. Wwe have a start value of type B, but now we have two functions instead of one. We have seqop and combop. 

* Seqop, represents a sequential operator, and like in foldLeft, it operates on two types. 
* Combop, And it represents a combination operator. And like in regular fold, it only operates on one type, in this case B. 

While the signature might seem complicated, it's actually great for parallelizing things over a cluster. In fact, it's considered to be even more general than fold or foldLeft because it's both parallelizable and it makes it possible to change the return type to something else. 

* foldLeft/foldRight also does the serial computation with order intact, this is something very hard to achieve in distributed computing frameworks. To maintain order in the distributed frameworks you will end up with lot synhronisations, data movements so and so forth. 

## Pair RDD's (Key-value pairs)

Key-val is the most used data structure in map/reduce framework.  

In spark key-val is Pair RDDs. 

``` scala
RDD[(K, V)]
```



Some of the special functions on pair RDD's are 

``` scala
def groupByKey(): RDD[(K, Iterable[V])]
def reduceByKey(func: (V, V) => V): RDD[(K,V)]
def join[W](other: RDD[(K, W)]): RDD[(K, (V,W))]
def mapValues
```

```Scala
def leftOuterJoin[W](other: RDD[(K, W)]): RDD[(K, (V, Option[W]))]
def rightOuterJoin[W](other: RDD[(K, W)]): RDD[(K, (Option[V], W))]
def fullOuterJoin[W](other: RDD[(K, W)]): RDD[(K, (Option[V], Option[W]))]
def join[W](other: RDD[(K, W)]): RDD[(K, (V, W))]
```



## Datasets 

``` scala
case class Listing(street: String, zip: Int, price: Int)
val listingDF = ... // DF of listing 

import org.apache.spark.sql.functions._
val averagePricesDF = listingDF.groupBy($"zip").avg("price")

averagePricesDF.collect()
// Array[org.apache.spark.sql.Row]
```

In dataframes all the items are of the type RDD[Row]. Even with collected results of dataframes, you will have to cast the `Row` with the expected data type; in this case we were expecting a Double (average of price), we will have to do castings as shown below. 

``` scala
val averagePriceAgain = averagePrices.map {
    row => (row(0).asInstanceOf[String], row(1).asInstanceOf[Double])
}
```



That's where `Dataset` come in. 

* Datasets can be thought of as `typed` distributed collection of data. 
* Dataset API unifies the Dataframe and RDD APIs.  
* Datasets requires structured/semi-structured data. `Schemas` and `Encoders` core part of Datasets

Dataframes are just special types of Datasets.

``` Scala
type DataFrame = DataSet[Row]
```

Datasets are a compromise between RDDs and DataFrames. We get more type information on datasets than on dataframes, and you get more optimizations on Datasets than you get on RDDs. 

``` scala
listingsDS.groupByKey( l => l.zip) // RDD like operations 
.agg(avg($"price").as[Double]) // DF like operators
```

As show abow Datasets enables writing both RDD and DF like operations on the same object, we can freely mix APIs. 

* Datasets add more typed operations
* Datasets lets you use higher-order functions like map, flatMap, filter...

### Transformations on Datasets 

The Dataset API includes both untyped and typed transformations. 

* **untyped transformations** the transformations we know from Dataframes
* **typed transformations** typed variants of many DataFrame transformations +  additional transformations such as RDD-like higher-order functions map, flatMap, etc. 

These APIs are integerated. You can call a map on a Dataframe and get back a Dataset, for example.

*Caveat: not every operation you know from RDDs are available on Datasets, and not all operations look 100% same on Datasets as they did on RDDs.*

``` scala
val keyValuesDF = List((2, "Me"), (1, "Thi"), (2, "Se")).toDF
val res = keyValuesDF.map(row => row(0).asInstanseOf[Int]+ 1)
```



A Dataset is a strongly typed collection of domain-specific objects that can be transformed in parallel using functional or relational operations. Each Dataset also has an untyped view called a `DataFrame`, which is a Dataset of [Row](https://spark.apache.org/docs/latest/api/scala/org/apache/spark/sql/Row.html).

Operations available on Datasets are divided into transformations and actions. Transformations are the ones that produce new Datasets, and actions are the ones that trigger computation and return results. Example transformations include map, filter, select, and aggregate (`groupBy`). Example actions count, show, or writing data out to file systems.

Datasets are "lazy", i.e. computations are only triggered when an action is invoked. Internally, a Dataset represents a logical plan that describes the computation required to produce the data. When an action is invoked, Spark's query optimizer optimizes the logical plan and generates a physical plan for efficient execution in a parallel and distributed manner. To explore the logical plan as well as optimized physical plan, use the `explain` function.

To efficiently support domain-specific objects, an [Encoder](https://spark.apache.org/docs/latest/api/scala/org/apache/spark/sql/Encoder.html) is required. The encoder maps the domain specific type `T` to Spark's internal type system. For example, given a class `Person` with two fields, `name` (string) and `age` (int), an encoder is used to tell Spark to generate code at runtime to serialize the `Person` object into a binary structure. This binary structure often has much lower memory footprint as well as are optimized for efficiency in data processing (e.g. in a columnar format). To understand the internal binary representation for data, use the `schema` function.