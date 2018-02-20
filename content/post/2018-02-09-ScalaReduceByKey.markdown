---
categories:
- posts
comments: true
date: 2018-02-09T00:00:00Z
tags:
- Scala
- reducebykey
- spark
title: Implementing reduceByKey in Scala
draft: true
---

### Problem Statement 
Implement a *Apache Spark* like reducebykey in scala. 

Given a input list like the one below one reduceByKey should operate a function on the values of same key with a function like the following one `(V, V) => f(V)`. And resulting List will have the combined values against each key. 
``` scala
val items =  List(("a",1), ("b",2), ("c",1), ("a", 3), ("c", 4))
```

A sample output if the combiner function is a `sum` function.

``` scala
val items =  List(("a",4), ("b",2), ("c",5))
```

A simple answer to this is using groupby in Scala. 

``` scala
items.groupBy(x => x ).map(x => (x._1, x._2.length))
```

