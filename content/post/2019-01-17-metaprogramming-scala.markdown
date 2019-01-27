---
categories:
- notes
comments: true
date: 2018-03-05T00:00:00Z
tags:
- functional programming
- Scala
- Spark
title: Metaprogramming
draft: true
---

# Metaprogramming: Macros and Reflection

*Metaprogramming is programming that manipulates programs as data.* 

- In some languages, the difference between programming and metaprogramming isn’t all that significant. Lisp dialects, for example, use the same S-expression representation for code and data, a property called *homoiconicity*. So, manipulating code is straightforward and not uncommon. 
- In statically typed languages like Java and Scala, metaprogramming is less common, but it’s still useful for solving many design problems.
- Scala’s metaprogramming support happens at compile time using a macro facility. Macros work more like constrained compiler plug-ins, because they **manipulate the abstract syntax tree (AST)** produced from the parsed source code. Macros are invoked to manipulate the AST before the final compilation phases leading to byte-code generation.
- The Java reflection library and Scala’s expanded library offer runtime reflection.
- Macros => Compile time, reflection apis => runtime

