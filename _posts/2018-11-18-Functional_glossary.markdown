---
layout: post
title: "Terms around functional Programming with Scala"
date: 2019-05-26 07:18:57
categories: ['learning']
tags: [Software Development, Scala, Functional Programming]
excerpt: |
  A glossary of functional programming terms that you should master in order to
  have a proficient reading of code and technical texts.
image:
  feature: glossary.png
---

I keep revisiting books for some definitions and details of many FP terms,
so I decided to write down a glossary as I did the [Scala Exercises](https://www.scala-exercises.org/).

This is largely imprecise, just a collection of notes that I'll keep improving and completing. Just don't take anything here too seriously, and go to the references instead :)

# Glossary

- **Algebraic data types**: data types defined with sealed trait and case classes. An algebraic daa type can be thought of as a set of possible values.
- **Currying**: is a transformation which converts a function `f` of two arguments into a function of one argument that partially applies `f`.
- **Higher-order functions**: functions that take other functions as arguments.
- **Kleisli arrow**: `A => F[B]` functions. Allows functions `f: A => M[B]` and `g: B => M[C]` to compose and yield `A => M[C]`, where `M` is a `Monad`. It's like ordinary composition but over effectful functions.
- **Semigroup**: a semigroup for some given type `A` has a single operation (which we will call `combine`), which takes two values of type `A`, and returns a value of type `A`. This operation must be guaranteed to be associative.
- **Tail-recursion**: a call is said to be in _tail position_ if the caller does nothing but returning the value. If all recursive calls made by a function are in tail position, Scala recompiles the recursion to iterative so it doesn't consume call stack frames.
- **Type bounds**:
  - *Upper bounds*: given `A <: Animal`, `A` can be instantiated only to type that conform to `Animal`. `A` is a subtype of `B`.
  - *Lower bounds*: given `A >: Repile`, `A` can range only supertypes of `Repile`.
  - *Mixed bounds*: given `A >: Zebra <: Animal`, `A` is restricted to a type on the interval between `Zebra` and `Animal`.
- **Type class**: the combination of parametrized types and implicit parameters.
- **Type variance**:
  - *Covariance*: `class C[+A]`. Roughly speaking, a type that accepts mutations of its elements should not be covariant. Covariant type parameters can only appear in method results.
  - *Contravariance*: `class C[-A]`. Functions are contravariant: if `A2 <: A1` and `B1 <: B2`, then `A1 => B1 <: A2 => B2`. Contravariant type parameters can only appear in method parameters.
  - *Nonvariace*: `class C[A]`. Invariant type parameters can appear anywhere.

# Additional notes

## Variance

If you think that grasping the concept of variance (covariance vs. contravariance) is hard, think about the example of animals and veterinaries:

```scala
  object VarianceExample {
    trait Animal
    class Mammal extends Animal
    class Zebra extends Mammal
  }

  object ContravarianceExample {
    object InvariantVet {
      abstract class Vet[A]

      def treatMammals(vet: Vet[Mammal]) = ???

      class AnimalVet extends Vet[Animal]

      // This won't compile because Vet is defined as invariant
      // "You may wish to define A as -A instead. (SLS 4.5)"
      //treatMammals(new AnimalVet)
    }

    object CovariantVet {
      abstract class Vet[+A]

      def treatMammal(vet: Vet[Mammal]) = ???

      class ZebraVet extends Vet[Zebra]

      // This compile but meaning is wrong! A veterinary who can only treat Zebras
      // shouldn't be able to treat any Mammal!!!
      treatMammal(new ZebraVet)
    }

    object ContravariantVet {
      // Better definition: a vet is defined by the subtypes that he can treat, not supertypes!
      abstract class Vet[-A]

      def treatMammals(vet: Vet[Mammal]) = ???

      class AnimalVet extends Vet[Animal]

      treatMammals(new AnimalVet)

      class ZebraVet extends Vet[Zebra]
      // This won't compile and it's ok: with contravariance we can define that a Zebra vet
      // can't treat Mammals
      // treatMammals(new ZebraVet)
    }
}
```

## Semigroups, functors...

### `Semigroup` > `Monoid`

- Semigroup on a type: associative `combine`.
- Monoid adds an `empty` method that returns a value that when combined with any other instance of that type returns the other instance.

### `Functor` > `Apply` > `Applicative` > `Monad`

- Functor: type class that abstracts over type constructors that can be map‘ed over (`F[A]`).
  - Obeys composition and identity laws.
  - Allows lifting `A => B` into a effectful `F[A] => F[B]`.
  - The `F` is often reffered as "effect" or computational context, functor abstracts over that.
- Apply: extends Functor with `ap`, similar to `map` but of type `F[A => B]`.
  - Encodes working with multiple independent effects.
- Applicative: extends Apply adding `pure` (returns a value in the context of the functor). It's a generalization of Monad, allowing expression of effectful computations in a pure functional way. Applicative is generally preferred to Monad when the structure of a computation is fixed a priori. That makes it possible to perform certain kinds of static analysis on applicative values.
- Monad: extends Applicative with flatten.

## Optics

- Iso: bijective function.
- Lens: zoom into products.
- Prism: zoom into sum types.
- Optional: filter + map.
- Transversal: generalization of optional to several targets.

# References

- **[FPS][FPS]**: [Functional Programming in Scala](https://www.goodreads.com/book/show/13541678-functional-programming-in-scala).
- **[FRDM][FRDM]**: [Functional and Reactive Domain Modeling](https://www.goodreads.com/book/show/23488413-functional-and-reactive-domain-modeling).
- **[SE][SE]**: [Scala Exercises](https://www.scala-exercises.org).

[FPS]: https://www.goodreads.com/book/show/13541678-functional-programming-in-scala "Functional Programming in Scala"
[FRDM]: https://www.goodreads.com/book/show/23488413-functional-and-reactive-domain-modeling "Functional and Reactive Domain Modeling"
[SE]: https://www.scala-exercises.org "Scala Exercises"
