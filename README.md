Poly
====

A simple ML dialect with definitions, let polymorphism and a fixpoint operator.
Uses syntax directed HM type inference.

Note
====
This is a modified version of Stephen Diehl's Poly.
https://github.com/sdiehl/write-you-a-haskell/tree/master/chapter7/poly_constraints

The modification was originally written by Kwang Yul Seo to implement an efficient Haskell interpreter in Haskell as he explained in his blog post [Write You An Interpreter](https://kseo.github.io/posts/2016-12-30-write-you-an-interpreter.html).

Seo's version compiles expressions into a finite, fixed set of combinators, and then runs these combinators as Haskell functions. This technique was introduced by Matthew Naylor in [The Monad Reader Issue 10, Evaluating Haskell in Haskell](https://wiki.haskell.org/wikiupload/0/0a/TMR-Issue10.pdf).

The current version is an attempt to make it compatible with the latest resolver and libraries, so it can be adapted to projects started today.

To compile and run:

```shell
$ stack build
$ stack exec poly
```

Usage:

```ocaml
Poly> let i x = x;
i : forall a. a -> a

Poly> i 3
3

Poly> :type i
i : forall a. a -> a

Poly> :type let k x y = x;
k : forall a b. a -> b -> a

Poly> :type let s f g x = f x (g x)
s : forall a b c. ((a -> b) -> c -> a) -> (a -> b) -> c -> b

Poly> :type let on g f = \x y -> g (f x) (f y)
on : forall a b c. (a -> a -> b) -> (c -> a) -> c -> c -> b

Poly> :type let let_bound = i (i i) (i 3)
let_bound : Int

Poly> :type let compose f g = \x -> f (g x)
compose : forall a b c. (a -> b) -> (c -> a) -> c -> b

Poly> let rec factorial n =
  if (n == 0)
  then 1
  else (n * (factorial (n-1)));
```

Notes
=====

Top level let declarations are syntactic sugar for nested lambda. For example:

```ocaml
let add x y = x + y;
```

Is semantically equivalent to:

```ocaml
let add = \x -> \y -> x + y;
```

Top level Let-rec declarations are syntactic sugar for use of the ``fix``
operator. For example:

```ocaml
let rec factorial n = if (n == 0) then 1 else (n * (factorial (n-1)));
```
Is semantically equivalent to:

```ocaml
let factorial = fix (\factorial n -> if (n == 0) then 1 else (n * (factorial (n-1))));
```

License
=======

Released under MIT license.
