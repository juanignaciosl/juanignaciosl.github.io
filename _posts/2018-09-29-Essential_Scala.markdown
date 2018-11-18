---
layout: post
title: "Essential Scala"
date: 2018-09-29 07:18:57
categories: ['Learning']
tags: [Software Development, Scala, books]
excerpt: |
  Notes about Essential Scala book.
image:
  feature: essential_scala.png
---

I haven't coded in Scala for some months and I wanted to refresh my memory,
which was a good excuse for picking [Essential Scala underscore
book](https://underscore.io/training/courses/essential-scala/) out of my
to-read shelf. It was just going to be a quick refresher read, but I
found more than what I expected and chose to write down some notes.

# Summary

Chapters 1, 2 and 3 are an introduction to Scala basics and syntax. Very
valuable and needed in a "Scala essentials" book.

Chapter 4 introduces Traits, but it doesn't stop on the syntax but digs into
patterns, such as Product Type or Sum Type pattern.

5 introduces functors and monads, which is something that you might not find in
a short introduction to Scala like this one.

6 explains the most important collection classes, ones that you'll probably be
used to if you've used Scala.

# MHO

This book guides you through Scala main features and patterns by means of short
exercises that emerge the need for abstractions. This is really didactic and
effective.

As concise and deep as it can be for a 250 page book. Don't be misled by the
name and size: it's not only about syntax but also about Scala patterns. As
Scala is a complex language with tons of features, this is priceless. Another
must-read for Scala developers.

# Notes

<details>
<summary markdown="1">
**Click** to see my collection of notes. It's not a summary at all but a mix of
highlights, definitions, important stuff or things that I didn't know.
</summary>

<div markdown="1">
### 4. Modelling Data with Traits

#### The Product Type Pattern

*Has-a and*: **product type**

If A has a b (with type B) and a c (with type C)

```scala
  case class A(b: B, c: C)
```

or

```scala
trait A {
  def b: B
  def c: C
}
```

#### The Sum Type Pattern

*Is-a or*: **sum type**.

```scala

sealed trait A
final case class B() extends A
final case class C() extends A
```

#### Algebraic Data Types

Let's see patterns beyond *has-a and* and *is-a or*.

*Is-a and*: A is a B and C:

```scala

trait B
trait C
trait A extends B with C
```

If we want to represent that some data conforms to a number of different
interfaces, instead of doing this you can be better off using a *type class*.
There are, however, several legitimate uses of this pattern:
- modularity, using what’s known as the cake pattern
- sharing implementation across several classes

*Has-a or*: A has a B or C. Two possible implementations:

```scala

trait A {
def d: D
}
sealed trait D
final case class B() extends D
final case class C() extends D
```

or

```scala

sealed trait A
final case class D(b: B) extends A
final case class E(c: C) extends A
```

#### Working With Data

> **Structural recursion** is the precise opposite of the process of building an
> algebraic data type. If A has a B and C
> (the product-type pattern), to construct an A we must have a B and a C.
> The sum and product type patterns tell
> us how to combine data to make bigger data. Structural recursion says that
> if we have an A as defined before,
> we must break it into its constituent B and C that we then combine in some
> way to get closer to our desired
> answer. Structural recursion is essentially the process of breaking down
> data into smaller pieces.

##### Structural Recursion using Polymorphism

**Product Type Polymorphism Pattern**:

> If A has a b (with type B) and a c (with type C), and we want to write a method
> f returning an F, simply write the method in the usual way.

```scala

case class A(b: B, c: C) {
  def f: F = ???
}
```

> In the body of the method we must use b, c, and any method parameters to
> construct the result of type F.

**Sum Type Polymorphism Pattern**:

> If A is a B or C, and we want to write a method f returning an F, define f as
> an abstract method on A and provide concrete implementations in B and C.

```scala

sealed trait A {
  def f: F
}

final case class B() extends A {
  def f: F = ???
}
final case class C() extends A {
  def f: F = ???
}
```

##### Structural Recursion using Pattern Matching

**Product Type Pattern Matching Pattern**:

> If A has a b (with type B) and a c (with type C), and we want to write a
method f that accepts an A and returns an F, write

```scala

def f(a: A): F =
  a match {
    case A(b, c) => ???
  } 
```

> In the body of the method we use b and c to construct the result of type F.

**Sum Type Pattern Matching Pattern**:

> If A is a B or C, and we want to write a method f accepting an A and returning
> an F, define a pattern matching case for B and C.

```scala

def f(a: A): F =
  a match {
  case B() => ???
  case C() => ???
```

> **The code follows the shape of the data, and can be produced in an almost
mechanical way**

> The general rule is: if a method only depends on other fields and methods in
> a class it is a good candidate to be implemented inside the class. If the
> method depends on other data (for example, if we needed a Cook to make
> dinner) consider implementing is using pattern matching outside of the
> classes in question. If we want to have more than one implementation we
> should use pattern matching and implement it outside the classes.

> **Recursive Algebraic Data Types Pattern**

> When defining recursive algebraic data types, there must be at least two
> cases: one that is recursive, and
> one that is not. Cases that are not recursive are known as base cases. In
> code, the general skeleton is:

```scala

sealed trait RecursiveExample
final case class RecursiveCase(recursion: RecursiveExample) extends RecursiveExample
final case object BaseCase extends RecursiveExample
```

> **Recursive Structural Recursion Pattern**:
> When writing structurally recursive code on a recursive algebraic data type:
> - whenever we encounter a recursive element in the data we make a recursive
> call to our method;
and
> - whenever we encounter a base case in the data we return the identity for
> the operation we are performing.

### 5. Sequencing Computations

> **Fold Pattern**:
> For an algebraic datatype A, fold converts it to a generic type B. Fold is a structural recursion with:
> - one function parameter for each case in A
> - each function takes as parameters the fields for its associated class;
> - if A is recursive, any function parameters that refer to a recursive field take a parameter of type B .
> The right-hand side of pattern matching cases, or the polymorphic methods as appropriate, consists of
> calls to the appropriate function.

#### Map, flatmap, functor, monads...

> A type like F[A] with a map method is called a functor. If a functor also has a flatMap method it is called a monad.

#### Variance

> **Covariant Generic Sum Type Pattern**
> If A of type T is a B or C , and C is not generic, write

```scala

sealed trait A[+T]
final case class B[T](t: T) extends A[T]
final case object C extends A[Nothing]
```

> This pattern extends to more than one type parameter. If a type parameter is not needed for a specific
> case of a sum type, we can substitute Nothing for that parameter.

> This pattern is the most commonly used one with generic sum types. We should only use covariant types where
> the container type is immutable. If the container allows mutation we should only use invariant types.

> **Contravariant Position Pattern**
> If A of a covariant type T and a method f of A complains that T is used in a contravariant position, introduce
a type TT >: T in f .

```scala

case class A[+T] {
  def f[TT >: T](t: TT): A[TT]
}
```

### 6. Collections

### 7. Type Classes

> Type Class Pa ern
> A type class is a trait with at least one type variable. The type variables specify the concrete types the
> type class instances are defined for. Methods in the trait usually use the type variables.

```scala

trait ExampleTypeClass[A] {
  def doSomething(in: A): Foo
}
```


> In Scala, a type class is just a trait. To use a type class we:
> - create implementations of that trait, called type class instances; and
> - typically we mark the type class instances as implicit values.

> Marking values as implicit tells the compiler it can supply them as a parameter to a method call if none is explicitly given. For the compiler to supply a value:
> 1. the parameter must be marked implicit in the method declaration;
> 2. there must be an implicit value available of the same type as the parameter; and
> 3. there must be only one such implicit value available.

> The compiler will look in the following places for Ordering instances:
> - the companion object of List ;
> - the companion object of Ordering ; and
> - the companion object of the type B , which is the type of elements in the list or any superclass.

> **Type Class Instance Packaging: Companion Objects**
> When defining a type class instance, if
> 1. there is a single instance for the type; and
> 2. you can edit the code for the type that you are defining the instance for
> then define the type class instance in the companion object of the type.

> **Type Class Instance Packaging: Companion Objects Part 2**
> When defining a type class instance, if
> 1. there is a single good default instance for the type; and
> 2. you can edit the code for the type that you are defining the instance for
> then define the type class instance in the companion object of the type. This allows users to override the
> instance by defining one in the local scope whilst s ll providing sensible default behaviour.

> There are four components of the type class pattern:
> - the actual type class itself;
> - the type class instances;
> - interfaces using implicit parameters; and
> - interfaces using enrichment and implicit parameters.

> **Type Class Interface Pattern**
> If the desired interface to a type class TypeClass is exactly the methods defined on the type class trait,
> define an interface on the companion object using a no-argument apply method like

```scala
object TypeClass {
  def apply[A](implicit instance: TypeClass[A]): TypeClass[A] = instance
}
```

**Type Class and Type Enrichment Pattern**

You can create an implicit class with the extra operations that you want to
add to a class instances. The implicit class constructor receives the instance,
and the operations receive implicitly the type instances. That allows invoking
the type-class pattern on any type we have an adapter as if it were a built-in
feature of the class.

> **Context Bound Syntax**
> A context bound is an annota on on a generic type variable like so:

```scala
[A : Context]
```

> It expands into a generic type parameter [A] along with an implicit parameter for a Context[A] .


</div>
</details>
