# Scala code examples for Functional Programming Jargon 

- This project is fork of [hemanth/functional-programming-jargon](https://github.com/hemanth/functional-programming-jargon)

# Functional Programming Jargon

Functional programming (FP) provides many advantages, and its popularity has been increasing as a result. However, each programming paradigm comes with its own unique jargon and FP is no exception. By providing a glossary, we hope to make learning FP easier.

Examples are presented in JavaScript (ES2015). [Why JavaScript?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)

*This is a [WIP](https://github.com/hemanth/functional-programming-jargon/issues/20); please feel free to send a PR ;)*

Where applicable, this document uses terms defined in the [Fantasy Land spec](https://github.com/fantasyland/fantasy-land)

__Translations__
* [Portuguese](https://github.com/alexmoreno/jargoes-programacao-funcional)
* [Spanish](https://github.com/idcmardelplata/functional-programming-jargon/tree/master)
* [Chinese](https://github.com/shfshanyue/fp-jargon-zh)
* [Bahasa Indonesia](https://github.com/wisn/jargon-pemrograman-fungsional)

__Table of Contents__
<!-- RM(noparent,notop) -->

* [Arity](#arity)
* [Higher-Order Functions (HOF)](#higher-order-functions-hof)
* [Partial Application](#partial-application)
* [Currying](#currying)
* [Closure](#closure)
* [Auto Currying](#auto-currying)
* [Function Composition](#function-composition)
* [Continuation](#continuation)
* [Purity](#purity)
* [Side effects](#side-effects)
* [Idempotent](#idempotent)
* [Point-Free Style](#point-free-style)
* [Predicate](#predicate)
* [Contracts](#contracts)
* [Category](#category)
* [Value](#value)
* [Constant](#constant)
* [Functor](#functor)
* [Pointed Functor](#pointed-functor)
* [Lift](#lift)
* [Referential Transparency](#referential-transparency)
* [Equational Reasoning](#equational-reasoning)
* [Lambda](#lambda)
* [Lambda Calculus](#lambda-calculus)
* [Lazy evaluation](#lazy-evaluation)
* [Monoid](#monoid)
* [Monad](#monad)
* [Comonad](#comonad)
* [Applicative Functor](#applicative-functor)
* [Morphism](#morphism)
  * [Endomorphism](#endomorphism)
  * [Isomorphism](#isomorphism)
* [Setoid](#setoid)
* [Semigroup](#semigroup)
* [Foldable](#foldable)
* [Lens](#lens)
* [Type Signatures](#type-signatures)
* [Algebraic data type](#algebraic-data-type)
  * [Sum type](#sum-type)
  * [Product type](#product-type)
* [Option](#option)
* [Functional Programming Libraries in Scala](#functional-programming-libraries-in-scala)


<!-- /RM -->

## Arity

The number of arguments a function takes. From words like unary, binary, ternary, etc. This word has the distinction of being composed of two suffixes, "-ary" and "-ity." Addition, for example, takes two arguments, and so it is defined as a binary function or a function with an arity of two. Such a function may sometimes be called "dyadic" by people who prefer Greek roots to Latin. Likewise, a function that takes a variable number of arguments is called "variadic," whereas a binary function must be given two and only two arguments, currying and partial application notwithstanding (see below).

```scala
val sum = (a : Int, b: Int) => a + b // The arity of sum is 2
// sum: (Int, Int) => Int = $$Lambda$3441/949288171@44a31d52
```

## Higher-Order Functions (HOF)

A function which takes a function as an argument and/or returns a function.


```scala
val filter = (predicate: Int => Boolean, xs: List[Int]) => xs.filter(predicate)
// filter: (Int => Boolean, List[Int]) => List[Int] = $$Lambda$3475/1472271008@7aca0ae9
```
```scala
val isEven = (x: Int) => x % 2 == 0
// isEven: Int => Boolean = $$Lambda$3476/858214604@4dd49ae1
```

```scala
filter(isEven, List(1, 2, 3, 4, 5))
// res0: List[Int] = List(2, 4)
```

## Partial Application

Partially applying a function means creating a new function by pre-filling some of the arguments to the original function.


```scala
// Something to apply
val add3 = (a: Int, b: Int, c: Int) => a + b + c
// add3: (Int, Int, Int) => Int = $$Lambda$3509/1963084265@4bc9845f

// Partially applying `2` and `3` to `add3` gives you a one-argument function
val fivePlus = add3(2, 3, _: Int) // (c) => 2 + 3 + c
// fivePlus: Int => Int = $$Lambda$3510/1476906319@3fc29e32

fivePlus(4)
// res3: Int = 9
```

Partial application helps create simpler functions from more complex ones by baking in data when you have it. [Curried](#currying) functions are automatically partially applied.

## Currying

The process of converting a function that takes multiple arguments into a function that takes them one at a time.

Each time the function is called it only accepts one argument and returns a function that takes one argument until all arguments are passed.

```scala
val sum = (a : Int, b: Int) => a + b
// sum: (Int, Int) => Int = $$Lambda$3511/415553975@21db0ea1

val curriedSum = (a: Int) => (b: Int) => a + b
// curriedSum: Int => (Int => Int) = $$Lambda$3522/552068472@5b4a9854

curriedSum(40)(2) // 42.
// res4: Int = 42

val add2 = curriedSum(2) // (b) => 2 + b
// add2: Int => Int = $$Lambda$3524/1243857026@2b245846

add2(10) // 12
// res5: Int = 12
```

## Closure

A closure is a way of accessing a variable outside its scope.
Formally, a closure is a technique for implementing lexically scoped named binding. It is a way of storing a function with an environment.

A closure is a scope which captures local variables of a function for access even after the execution has moved out of the block in which it is defined.
ie. they allow referencing a scope after the block in which the variables were declared has finished executing.


```scala
val addTo = (x :Int) => (y: Int) => x + y
// addTo: Int => (Int => Int) = $$Lambda$3525/1093452726@29b2ed8f

val addToFive = addTo(6)
// addToFive: Int => Int = $$Lambda$3526/2089810407@1e32b4bb

addToFive(3) // returns 8
// res6: Int = 9
```

The function `addTo()` returns a function(internally called `add()`), lets store it in a variable called `addToFive` with a curried call having parameter 5.

Ideally, when the function `addTo` finishes execution, its scope, with local variables add, x, y should not be accessible. But, it returns 8 on calling `addToFive()`. This means that the state of the function `addTo` is saved even after the block of code has finished executing, otherwise there is no way of knowing that `addTo` was called as `addTo(5)` and the value of x was set to 5.

Lexical scoping is the reason why it is able to find the values of x and add - the private variables of the parent which has finished executing. This value is called a Closure.

The stack along with the lexical scope of the function is stored in form of reference to the parent. This prevents the closure and the underlying variables from being garbage collected(since there is at least one live reference to it).

Lambda Vs Closure: A lambda is essentially a function that is defined inline rather than the standard method of declaring functions. Lambdas can frequently be passed around as objects.

A closure is a function that encloses its surrounding state by referencing fields external to its body. The enclosed state remains across invocations of the closure.


__Further reading/Sources__
* [Lambda Vs Closure](http://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)
* [How do JavaScript Closures Work?](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)

## Auto Currying
Transforming a function that takes multiple arguments into one that if given less than its correct number of arguments returns a function that takes the rest. When the function gets the correct number of arguments it is then evaluated.

lodash & Ramda have a `curry` function that works this way.

```scala
val add = (x: Int, y: Int) => x + y
// add: (Int, Int) => Int = $$Lambda$3527/2124742392@72e5a2c5

val curriedAdd = add.curried
// curriedAdd: Int => (Int => Int) = scala.Function2$$Lambda$3528/149489050@79f9315c

curriedAdd(2) // (y) => 1 + y
// res7: Int => Int = scala.Function2$$Lambda$3529/677277518@3eb26ae2

curriedAdd(1)(2) // 3
// res8: Int = 3
```

__Further reading__
* [Favoring Curry](http://fr.umio.us/favoring-curry/)
* [Hey Underscore, You're Doing It Wrong!](https://www.youtube.com/watch?v=m3svKOdZijA)

## Function Composition

The act of putting two functions together to form a third function where the output of one function is the input of the other.

```scala
def compose[A, B, C](f: B => C, g: A => B) = (a: A) => f(g(a)) // Definition
// compose: [A, B, C](f: B => C, g: A => B)A => C

val floorAndToString = compose((x: Double) => x.toString, math.floor) // Usage
// floorAndToString: Double => String = $$Lambda$3550/1401633659@2d40165e

floorAndToString(121.212121) // '121.0'
// res9: String = 121.0
```

## Continuation

At any given point in a program, the part of the code that's yet to be executed is known as a continuation.

```scala
def printAsString(num: Int) = println(s"Given $num")
// printAsString: (num: Int)Unit

val printAsString= (num: Int) => println(s"Given $num")
// printAsString: Int => Unit = $$Lambda$3551/1418297608@415c59c7

val addOneAndContinue = (num: Int, cc: Int => Any) => {
  val result = num + 1
  cc(result)
}
// addOneAndContinue: (Int, Int => Any) => Any = $$Lambda$3555/1288068304@5fc90830

addOneAndContinue(2, printAsString) 
// Given 3
// res10: Any = ()
```

Continuations are often seen in asynchronous programming when the program needs to wait to receive data before it can continue. The response is often passed off to the rest of the program, which is the continuation, once it's been received.

```scala
def continueProgramWith(data: String) = {
  // Continues program with data
}
// continueProgramWith: (data: String)Unit

def readFileAsync(file: String, cb: (Option[Throwable], String) => Unit) = {}
// readFileAsync: (file: String, cb: (Option[Throwable], String) => Unit)Unit

readFileAsync("path/to/file", (err, response) => {
  if (err.isDefined) {
    // handle error
    ()
  }
  continueProgramWith(response)
})
```

## Purity

A function is pure if the return value is only determined by its
input values, and does not produce side effects.

```scala
val greet = (name: String) => s"Hi, ${name}"
// greet: String => String = $$Lambda$3557/636255488@26576443

greet("Brianne")
// res12: String = Hi, Brianne
```

As opposed to each of the following:

```scala
var name = "Brianne"
// name: String = Brianne

def greet = () => s"Hi, ${name}"
// greet: () => String

greet() 
// res13: String = Hi, Brianne
```

The above example's output is based on data stored outside of the function...

```scala
var greeting: String = _
// greeting: String = null

val greet = (name: String) => {
  greeting = s"Hi, ${name}"
}
// greet: String => Unit = $$Lambda$3559/1623698257@69e3a8b8

greet("Brianne")

greeting 
// res15: String = Hi, Brianne
```

... and this one modifies state outside of the function.

## Side effects

A function or expression is said to have a side effect if apart from returning a value, it interacts with (reads from or writes to) external mutable state.

```scala
import java.util.Date
// import java.util.Date

def differentEveryTime = new Date()
// differentEveryTime: java.util.Date
```

```scala
println("IO is a side effect!")
// IO is a side effect!
```

## Idempotent

A function is idempotent if reapplying it to its result does not produce a different result.

```
f(f(x)) ≍ f(x)
```

```scala
math.abs(math.abs(10))
// res17: Int = 10
```

```scala
def sort[A: Ordering](xs: List[A]) = xs.sorted
// sort: [A](xs: List[A])(implicit evidence$1: Ordering[A])List[A]

sort(sort(sort(List(2, 1))))
// res18: List[Int] = List(1, 2)
```

## Point-Free Style

Writing functions where the definition does not explicitly identify the arguments used. This style usually requires [currying](#currying) or other [Higher-Order functions](#higher-order-functions-hof). A.K.A Tacit programming.

```scala
// Given
def map[A, B](fn: A => B) = (list: List[A]) => list.map(fn)
// map: [A, B](fn: A => B)List[A] => List[B]

val add = (a: Int) => (b: Int) => a + b
// add: Int => (Int => Int) = $$Lambda$3577/151117934@2e344492

// Then

// Not points-free - `numbers` is an explicit argument
val incrementAll = (numbers: List[Int]) => map(add(1))(numbers)
// incrementAll: List[Int] => List[Int] = $$Lambda$3578/487446095@43212328

// Points-free - The list is an implicit argument
val incrementAll2 = map(add(1))
// incrementAll2: List[Int] => List[Int] = $$Lambda$3580/596896842@24345e32
```

`incrementAll` identifies and uses the parameter `numbers`, so it is not points-free.  `incrementAll2` is written just by combining functions and values, making no mention of its arguments.  It __is__ points-free.

Points-free function definitions look just like normal assignments without `function` or `=>`.

## Predicate
A predicate is a function that returns true or false for a given value. A common use of a predicate is as the callback for array filter.

```scala
val predicate = (a: Int) => a > 2
// predicate: Int => Boolean = $$Lambda$3581/971238461@65706561

List(1, 2, 3, 4).filter(predicate)
// res24: List[Int] = List(3, 4)
```

## Contracts

A contract specifies the obligations and guarantees of the behavior from a function or expression at runtime. This acts as a set of rules that are expected from the input and output of a function or expression, and errors are generally reported whenever a contract is violated.

```scala
scala> // Define our contract : int -> int
     | def contract[A](input: A): Boolean = input match {
     |   case _ : Int => true 
     |   case _ => throw new Exception("Contract violated: expected int -> int")
     | }
contract: [A](input: A)Boolean

scala> def addOne[A](num: A): Int = {
     |   contract(num)
     |   num.asInstanceOf[Int] + 1
     | }
addOne: [A](num: A)Int

scala> addOne(2) // 3
res26: Int = 3

scala> addOne("some string") // Contract violated: expected int -> int
java.lang.Exception: Contract violated: expected int -> int
  at .contract(<console>:16)
  at .addOne(<console>:15)
  ... 43 elided
```

## Category

A category in category theory is a collection of objects and morphisms between them. In programming, typically types
act as the objects and functions as morphisms.

To be a valid category 3 rules must be met:

1. There must be an identity morphism that maps an object to itself.
    Where `a` is an object in some category,
    there must be a function from `a -> a`.
2. Morphisms must compose.
    Where `a`, `b`, and `c` are objects in some category,
    and `f` is a morphism from `a -> b`, and `g` is a morphism from `b -> c`;
    `g(f(x))` must be equivalent to `(g • f)(x)`.
3. Composition must be associative
    `f • (g • h)` is the same as `(f • g) • h`

Since these rules govern composition at very abstract level, category theory is great at uncovering new ways of composing things.

__Further reading__

* [Category Theory for Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)

## Value

Anything that can be assigned to a variable.

```scala
case class Person(name: String, age: Int)
// defined class Person

5
// res28: Int = 5

Person("John", 30) 
// res29: Person = Person(John,30)

(a: Any) => a
// res30: Any => Any = $$Lambda$3803/583264391@5daca47e

List(1)
// res31: List[Int] = List(1)

null
// res32: Null = null
```

## Constant

A variable that cannot be reassigned once defined.

```scala
val five = 5
// five: Int = 5

val john = Person("John", 30)
// john: Person = Person(John,30)
```

Constants are [referentially transparent](#referential-transparency). That is, they can be replaced with the values that they represent without affecting the result.

With the above two constants the following expression will always return `true`.

```scala
john.age + five == Person("John", 30).age + 5
// res33: Boolean = true
```

## Functor

An object that implements a `map` function which, while running over each value in the object to produce a new object, adheres to two rules:

__Preserves identity__
```
object.map(x => x) ≍ object
```

__Composable__

```
object.map(compose(f, g)) ≍ object.map(g).map(f)
```

(`f`, `g` are arbitrary functions)

A common functor in JavaScript is `Array` since it abides to the two functor rules:

```scala
List(1, 2, 3).map(x => x) 
// res34: List[Int] = List(1, 2, 3)
```

and

```scala
val f = (x: Int) => x + 1
// f: Int => Int = $$Lambda$3805/1337520858@1bbd1348

val g = (x: Int) => x * 2
// g: Int => Int = $$Lambda$3806/1201030873@587ed78f

List(1, 2, 3).map(x => f(g(x)))
// res35: List[Int] = List(3, 5, 7)

List(1, 2, 3).map(g).map(f)    
// res36: List[Int] = List(3, 5, 7)
```

## Pointed Functor
An Applicative with an `pure` function that puts _any_ single value into it.

[cats](https://typelevel.org/cats/) adds [`Applicative#pure`](https://github.com/typelevel/cats/blob/31874ce42b7e98bbb436a430c6a708f8e9db0e6d/core/src/main/scala/cats/Applicative.scala#L23) making arrays a pointed functor.

```scala
import cats._
// import cats._

import cats.implicits._
// import cats.implicits._

Applicative[List].pure(1)
// res37: List[Int] = List(1)
```

## Lift

Lifting is when you take a value and put it into an object like a [functor](#pointed-functor). If you lift a function into an [Applicative Functor](#applicative-functor) then you can make it work on values that are also in that functor.

Some implementations have a function called `lift`, or `liftA2` to make it easier to run functions on functors.

```scala
def liftA2[F[_]: Monad, A, B, C](f: A => B => C)(a: F[A], b: F[B]) = {
  a.map(f).ap(b)
}
// warning: there was one feature warning; for details, enable `:setting -feature' or `:replay -feature'
// liftA2: [F[_], A, B, C](f: A => (B => C))(a: F[A], b: F[B])(implicit evidence$1: cats.Monad[F])F[C]

val mult = (a: Int) => (b: Int) => a * b
// mult: Int => (Int => Int) = $$Lambda$3825/941006982@39462c4

val liftedMult = liftA2[List, Int, Int, Int](mult) _
// liftedMult: (List[Int], List[Int]) => List[Int] = $$Lambda$3829/272469990@ca099d2

liftedMult(List(1, 2), List(3))
// res38: List[Int] = List(3, 6)

liftA2((a: Int) => (b: Int) => a + b)(List(1, 2), List(3, 4))
// res39: List[Int] = List(4, 5, 5, 6)
```

Lifting a one-argument function and applying it does the same thing as `map`.

```scala
val increment = (x: Int) => x + 1
// increment: Int => Int = $$Lambda$3834/808500214@a661284

Applicative[List].lift(increment)(List(2))
// res40: List[Int] = List(3)

List(2).map(increment)
// res41: List[Int] = List(3)
```


## Referential Transparency

An expression that can be replaced with its value without changing the
behavior of the program is said to be referentially transparent.

Say we have function greet:

```scala
val greet = () => "Hello World!"
// greet: () => String = $$Lambda$3836/2079217133@268fc129
```

Any invocation of `greet()` can be replaced with `Hello World!` hence greet is
referentially transparent.

##  Equational Reasoning

When an application is composed of expressions and devoid of side effects, truths about the system can be derived from the parts.

## Lambda

An anonymous function that can be treated like a value.

```scala
(_: Int) + 1
// res42: Int => Int = $$Lambda$3837/1224602150@11727d99

(x: Int) => x + 1
// res43: Int => Int = $$Lambda$3838/2083153440@e93f841
```
Lambdas are often passed as arguments to Higher-Order functions.

```scala
List(1, 2).map(_ + 1)
// res44: List[Int] = List(2, 3)
```

You can assign a lambda to a variable.

```scala
val add1 = (a: Int) => a + 1
// add1: Int => Int = $$Lambda$3840/120904550@65f0d087
```

## Lambda Calculus
A branch of mathematics that uses functions to create a [universal model of computation](https://en.wikipedia.org/wiki/Lambda_calculus).

## Lazy evaluation

Lazy evaluation is a call-by-need evaluation mechanism that delays the evaluation of an expression until its value is needed. In functional languages, this allows for structures like infinite lists, which would not normally be available in an imperative language where the sequencing of commands is significant.

```scala
lazy val rand: Double = {
  println("generate random value...")
  math.random()
}
// rand: Double = <lazy>
```

```scala
rand // Each execution gives a random value, expression is evaluated on need.
// generate random value...
// res45: Double = 0.20649400778816496
```

## Monoid

An object with a function that "combines" that object with another of the same type.

One simple monoid is the addition of numbers:

```scala
1 + 1 
// res46: Int = 2
```
In this case number is the object and `+` is the function.

An "identity" value must also exist that when combined with a value doesn't change it.

The identity value for addition is `0`.
```scala
1 + 0 
// res47: Int = 1
```

It's also required that the grouping of operations will not affect the result (associativity):

```scala
1 + (2 + 3) == (1 + 2) + 3
// res48: Boolean = true
```

Array concatenation also forms a monoid:

```scala
List(1, 2) ::: List(3, 4)
// res49: List[Int] = List(1, 2, 3, 4)
```

The identity value is empty array `[]`

```scala
List(1, 2) ::: List()
// res50: List[Int] = List(1, 2)
```

If identity and compose functions are provided, functions themselves form a monoid:

```scala
def identity[A](a: A): A = a
// identity: [A](a: A)A

def compose[A, B, C](f: B => C, g: A => B) = (a: A) => f(g(a)) // Definition
// compose: [A, B, C](f: B => C, g: A => B)A => C
```
`foo` is any function that takes one argument.
```
compose(foo, identity) ≍ compose(identity, foo) ≍ foo
```

## Monad

A monad is an object with [`pure`](#pointed-functor) and `flatMap` functions. `flatMap` is like [`map`](#functor) except it un-nests the resulting nested object.

```scala
// Implementation
trait Monad[F[_]] {
  def pure[A](a: A): F[A]
  def flatMap[A, B](fa: F[A])(f: A => F[B]): F[B]
  def map[A, B](fa: F[A])(f: A => B): F[B]
}
// warning: there was one feature warning; for details, enable `:setting -feature' or `:replay -feature'
// defined trait Monad
// warning: previously defined object Monad is not a companion to trait Monad.
// Companions must be defined together; you may wish to use :paste mode for this.

object Monad {
  def apply[F[_]](implicit ev: Monad[F]) = ev
  
  implicit val listInstance: Monad[List] = new Monad[List] {
    def pure[A](x: A) = List(x)

    def flatMap[A, B](fa: List[A])(f: A => List[B]): List[B] = 
      fa.foldLeft(List[B]()) { (acc, x) => acc ::: f(x) }
      
    def map[A, B](fa: List[A])(f: A => B): List[B] =
      flatMap(fa)(x => pure(f(x)))
      
  }
}
// warning: there was one feature warning; for details, enable `:setting -feature' or `:replay -feature'
// defined object Monad
// warning: previously defined trait Monad is not a companion to object Monad.
// Companions must be defined together; you may wish to use :paste mode for this.

import Monad._
// import Monad._

// Usage
Monad[List].flatMap(List("cat,dog", "fish,bird"))(a => a.split(",").toList)
// res53: List[String] = List(cat, dog, fish, bird)

// Contrast to map
Monad[List].map(List("cat,dog", "fish,bird"))(a => a.split(",").toList)
// res55: List[List[String]] = List(List(cat, dog), List(fish, bird))
```

`pure` is also known as `return` in other functional languages.
`flatMap` is also known as `bind` in other languages.

## Comonad

An object that has `extract` and `coflatMap` functions.

```scala
trait Comonad[F[_]] {
  def extract[A](x: F[A]): A
  def coflatMap[A, B](fa: F[A])(f: F[A] => B): F[B]
}
// warning: there was one feature warning; for details, enable `:setting -feature' or `:replay -feature'
// defined trait Comonad
// warning: previously defined object Comonad is not a companion to trait Comonad.
// Companions must be defined together; you may wish to use :paste mode for this.

type Id[X] = X
// defined type alias Id

def id[X](x: X): Id[X] = x
// id: [X](x: X)Id[X]

object Comonad {
  def apply[F[_]](implicit ev: Comonad[F]) = ev

  implicit val idInstance: Comonad[Id] = new Comonad[Id] {
    def extract[A](x: Id[A]): A = x
    def coflatMap[A, B](fa: Id[A])(f: Id[A] => B): Id[B] = {
      id(f(fa))
    }
  }
}
// warning: there was one feature warning; for details, enable `:setting -feature' or `:replay -feature'
// defined object Comonad
// warning: previously defined trait Comonad is not a companion to object Comonad.
// Companions must be defined together; you may wish to use :paste mode for this.
```

Extract takes a value out of a functor.

```scala
import Comonad._
// import Comonad._

Comonad[Id].extract(id(1))
// res56: Id[Int] = 1
```

Extend runs a function on the comonad. The function should return the same type as the comonad.

```scala
Comonad[Id].coflatMap[Int, Int](id(1))(co => Comonad[Id].extract(co) + 1)
// res57: Id[Int] = 2
```

## Applicative Functor

An applicative functor is an object with an `ap` function. `ap` applies a function in the object to a value in another object of the same type.

```scala
// Implementation
trait Applicative[F[_]] {
  def ap[A, B](ff: F[A => B])(fa: F[A]): F[B]
}
// warning: there was one feature warning; for details, enable `:setting -feature' or `:replay -feature'
// defined trait Applicative
// warning: previously defined object Applicative is not a companion to trait Applicative.
// Companions must be defined together; you may wish to use :paste mode for this.

object Applicative {
  def apply[F[_]](implicit ev: Applicative[F]) = ev
  
  implicit val listInstance = new Applicative[List] {
    def ap[A, B](ff: List[A => B])(fa: List[A]): List[B] =
      ff.foldLeft(List[B]()) { (acc, f) => acc ::: fa.map(f) }
  }
}
// warning: there was one feature warning; for details, enable `:setting -feature' or `:replay -feature'
// defined object Applicative
// warning: previously defined trait Applicative is not a companion to object Applicative.
// Companions must be defined together; you may wish to use :paste mode for this.

import Applicative._
// import Applicative._

// Example usage
Applicative[List].ap(List((_: Int) + 1))(List(1))
// res60: List[Int] = List(2)
```

This is useful if you have two objects and you want to apply a binary function to their contents.

```scala
// Arrays that you want to combine
val arg1 = List(1, 3)
// arg1: List[Int] = List(1, 3)

val arg2 = List(4, 5)
// arg2: List[Int] = List(4, 5)

// combining function - must be curried for this to work
val add = (x: Int) => (y: Int) => x + y
// add: Int => (Int => Int) = $$Lambda$3880/964425282@54b7810

val partiallyAppiedAdds = Applicative[List].ap(List(add))(arg1) // [(y) => 1 + y, (y) => 3 + y]
// partiallyAppiedAdds: List[Int => Int] = List($$Lambda$3881/1355427055@4a6e22e5, $$Lambda$3881/1355427055@259a58bd)
```

This gives you an array of functions that you can call `ap` on to get the result:

```scala
Applicative[List].ap(partiallyAppiedAdds)(arg2)
// res63: List[Int] = List(5, 6, 7, 8)
```

## Morphism

A transformation function.

### Endomorphism

A function where the input type is the same as the output.

```scala
// uppercase :: String -> String
val uppercase = (str: String) => str.toUpperCase
// uppercase: String => String = $$Lambda$3886/1908696012@67473ffa

// decrement :: Number -> Number
val decrement = (x: Int) => x - 1
// decrement: Int => Int = $$Lambda$3887/1569202064@44e51e92
```

### Isomorphism

A pair of transformations between 2 types of objects that is structural in nature and no data is lost.

For example, 2D coordinates could be stored as an array `[2,3]` or object `{x: 2, y: 3}`.

```scala
// Providing functions to convert in both directions makes them isomorphic.
case class Coords(x: Int, y: Int)
// defined class Coords

val pairToCoords = (pair: (Int, Int)) => Coords(pair._1, pair._2)
// pairToCoords: ((Int, Int)) => Coords = $$Lambda$3888/672125148@319f15a4

val coordsToPair = (coods: Coords) => (coods.x, coods.y)
// coordsToPair: Coords => (Int, Int) = $$Lambda$3889/121823381@7659efd9

coordsToPair(pairToCoords((1, 2)))
// res67: (Int, Int) = (1,2)

pairToCoords(coordsToPair(Coords(1, 2)))
// res68: Coords = Coords(1,2)
```



## Setoid

An object that has an `equals` function which can be used to compare other objects of the same type.

Make array a setoid:

```scala
trait Eq[A] {
  def eqv(x: A, y: A): Boolean
}
// defined trait Eq

object Eq {
  def apply[A](implicit ev: Eq[A]) = ev

  implicit def arrayInstance[B]: Eq[Array[B]] = new Eq[Array[B]] {
    def eqv(xs: Array[B], ys: Array[B]): Boolean =
      xs.zip(ys).foldLeft(true) {
        case (isEq, (x, y)) => isEq && x == y
      }
  }
  
  implicit class EqOps[A](x: A) {
    def eqv(y: A)(implicit ev: Eq[A]) =
      ev.eqv(x, y)
  }
}
// defined object Eq
// warning: previously defined trait Eq is not a companion to object Eq.
// Companions must be defined together; you may wish to use :paste mode for this.

import Eq._
// import Eq._

Array(1, 2) == Array(1, 2)
// res69: Boolean = false

Array(1, 2).eqv(Array(1, 2))
// res70: Boolean = true

Array(1, 2).eqv(Array(0))
// res71: Boolean = false
```

## Semigroup

An object that has a `combine` function that combines it with another object of the same type.

```scala
trait Semigroup[A] {
  def combine(x: A, y: A): A
}
// defined trait Semigroup

object Semigroup {
  def apply[A](implicit ev: Semigroup[A]) = ev

  implicit def listInstance[B]: Semigroup[List[B]] = new Semigroup[List[B]] {
    def combine(x: List[B], y: List[B]): List[B] = x ::: y
  }

}
// defined object Semigroup
// warning: previously defined trait Semigroup is not a companion to object Semigroup.
// Companions must be defined together; you may wish to use :paste mode for this.

import Semigroup._
// import Semigroup._

Semigroup[List[Int]].combine(List(1), List(2))
// res72: List[Int] = List(1, 2)
```

## Foldable

An object that has a `reduce` function that can transform that object into some other type.

```scala
def sum[A](xs: List[A])(implicit N: Numeric[A]) : A =
  Foldable[List].foldLeft(xs, N.zero) {
    case (acc, x) => N.plus(acc, x)
  }
// sum: [A](xs: List[A])(implicit N: Numeric[A])A

sum(List(1, 2, 3))
// res73: Int = 6
```

## Lens ##
A lens is a structure (often an object or function) that pairs a getter and a non-mutating setter for some other data
structure.

```scala
// Using [Monocle's lens](https://github.com/julien-truffaut/Monocle)
import monocle.Lens
// import monocle.Lens

case class Person(name: String)
// defined class Person

val nameLens = Lens[Person, String](_.name)(str => p => p.copy(name = str))
// nameLens: monocle.Lens[Person,String] = monocle.PLens$$anon$8@3ece5ad8
```

Having the pair of get and set for a given data structure enables a few key features.

```scala
val person = Person("Gertrude Blanch")
// person: Person = Person(Gertrude Blanch)

// invoke the getter
nameLens.get(person)
// res76: String = Gertrude Blanch

// invoke the setter
nameLens.set("Shafi Goldwasser")(person)
// res78: Person = Person(Shafi Goldwasser)

// run a function on the value in the structure
nameLens.modify(_.toUpperCase)(person)
// res80: Person = Person(GERTRUDE BLANCH)
```

Lenses are also composable. This allows easy immutable updates to deeply nested data.

```scala
// This lens focuses on the first item in a non-empty array

def firstLens[A] = Lens[List[A], A] {
  // get first item in array
  _.head
} { 
  // non-mutating setter for first item in array
  x => xs => x :: xs.tail
}
// firstLens: [A]=> monocle.Lens[List[A],A]

val people = List(Person("Gertrude Blanch"), Person("Shafi Goldwasser"))
// people: List[Person] = List(Person(Gertrude Blanch), Person(Shafi Goldwasser))

// Despite what you may assume, lenses compose left-to-right.
(firstLens composeLens nameLens).modify(_.toUpperCase)(people)
// res84: List[Person] = List(Person(GERTRUDE BLANCH), Person(Shafi Goldwasser))
```

Other implementations:
* [Quicklens](https://github.com/adamw/quicklens) - Modify deeply nested case class fields
* [Sauron](https://github.com/pathikrit/sauron) - Yet another Scala lens macro, Lightweight lens library in less than 50-lines of Scala
* [scalaz.Lens](http://eed3si9n.com/learning-scalaz/Lens.html)
                                                                                


## Type Signatures 

Every functions in Scala will indicate the types of their arguments and return values.

```scala
// functionName :: firstArgType -> secondArgType -> returnType

// add :: Number -> Number -> Number
val add = (x: Int) => (y: Int) => x + y
// add: Int => (Int => Int) = $$Lambda$3992/1013307953@1445ea9

// increment :: Number -> Number
val increment = (x: Int) => x + 1
// increment: Int => Int = $$Lambda$3993/1324354565@3bebe5cc
```

If a function accepts another function as an argument it is wrapped in parentheses.

```scala
// call :: (a -> b) -> a -> b
def call[A, B] = (f: A => B) => (x: A) => f(x)
// call: [A, B]=> (A => B) => (A => B)
```

The letters `a`, `b`, `c`, `d` are used to signify that the argument can be of any type. The following version of `map` takes a function that transforms a value of some type `a` into another type `b`, an array of values of type `a`, and returns an array of values of type `b`.

```scala
// map :: (a -> b) -> [a] -> [b]
def map[A, B] = (f: A => B) => (list: List[A]) => list.map(f)
// map: [A, B]=> (A => B) => (List[A] => List[B])
```

__Further reading__
* [Ramda's type signatures](https://github.com/ramda/ramda/wiki/Type-Signatures)
* [Mostly Adequate Guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html#whats-your-type)
* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) on Stack Overflow

## Algebraic data type
A composite type made from putting other types together. Two common classes of algebraic types are [sum](#sum-type) and [product](#product-type).

### Sum type
A Sum type is the combination of two types together into another one. It is called sum because the number of possible values in the result type is the sum of the input types.

we can use `sealed trait` or `Either` to have this type:
```scala
// imagine that rather than sets here we have types that can only have these values
sealed trait Bool
// defined trait Bool

object True extends Bool
// defined object True

object False extends Bool
// defined object False

sealed trait HalfTrue
// defined trait HalfTrue

object HalfTrue extends HalfTrue
// defined object HalfTrue
// warning: previously defined trait HalfTrue is not a companion to object HalfTrue.
// Companions must be defined together; you may wish to use :paste mode for this.

// The weakLogic type contains the sum of the values from bools and halfTrue
type WeakLogicType = Either[Bool, HalfTrue]
// defined type alias WeakLogicType

val weakLogicValues: Set[Either[HalfTrue, Bool]] = Set(Right(True), Right(False), Left(HalfTrue))
// weakLogicValues: Set[Either[HalfTrue,Bool]] = Set(Right(True$@6063925f), Right(False$@f185855), Left(HalfTrue$@89ed2a7))
```

Sum types are sometimes called union types, discriminated unions, or tagged unions.

There's a [couple](https://github.com/paldepind/union-type) [libraries](https://github.com/puffnfresh/daggy) in JS which help with defining and using union types.

Flow includes [union types](https://flow.org/en/docs/types/unions/) and TypeScript has [Enums](https://www.typescriptlang.org/docs/handbook/enums.html) to serve the same role.

### Product type

A **product** type combines types together in a way you're probably more familiar with:

```scala
// point :: (Number, Number) -> {x: Number, y: Number}
case class Point(x: Int, y: Int)
// defined class Point

val point = (x: Int, y: Int) => Point(x, y)
// point: (Int, Int) => Point = $$Lambda$3995/1369656752@61e7f147
```
It's called a product because the total possible values of the data structure is the product of the different values. Many languages have a tuple type which is the simplest formulation of a product type.

See also [Set theory](https://en.wikipedia.org/wiki/Set_theory).

## Option
Option is a [sum type](#sum-type) with two cases often called `Some` and `None`.

Option is useful for composing functions that might not return a value.

```scala
// Naive definition

trait MyOption[+A] {
  def map[B](f: A => B): MyOption[B]
  def flatMap[B](f: A => MyOption[B]): MyOption[B]
}
// defined trait MyOption

case class MySome[A](a: A) extends MyOption[A] {
  def map[B](f: A => B): MyOption[B] = MySome(f(a))
  def flatMap[B](f: A => MyOption[B]): MyOption[B] = f(a)
}
// defined class MySome

case object MyNone extends MyOption[Nothing] {
  def map[B](f: Nothing => B): MyOption[B] = this
  def flatMap[B](f: Nothing => MyOption[B]) = this
}
// defined object MyNone

// maybeProp :: (String, {a}) -> Option a
def maybeProp[A, B](key: A, obj: Map[A, B]): Option[B] = obj.get(key)
// maybeProp: [A, B](key: A, obj: Map[A,B])Option[B]
```
Use `flatMap` to sequence functions that return `Option`s
```scala
// getItem :: Cart -> Option CartItem
def getItem[A](cart: Map[String, Map[String, A]]): Option[Map[String, A]] = maybeProp("item", cart)
// getItem: [A](cart: Map[String,Map[String,A]])Option[Map[String,A]]

// getPrice :: Item -> Option Number
def getPrice[A](item: Map[String, A]): Option[A] = maybeProp("price", item)
// getPrice: [A](item: Map[String,A])Option[A]

// getNestedPrice :: cart -> Option a
def getNestedPrice[A](cart: Map[String, Map[String, A]]) = getItem(cart).flatMap(getPrice)
// getNestedPrice: [A](cart: Map[String,Map[String,A]])Option[A]

getNestedPrice(Map())
// res100: Option[Nothing] = None

getNestedPrice(Map("item" -> Map("foo" -> 1)))
// res101: Option[Int] = None

getNestedPrice(Map("item" -> Map("price" -> 9.99)))
// res102: Option[Double] = Some(9.99)
```

`Option` is also known as `Maybe`. `Some` is sometimes called `Just`. `None` is sometimes called `Nothing`.

## Functional Programming Libraries in Scala

* [cats](https://typelevel.org/cats/)
* [scalaz](https://github.com/scalaz/scalaz)
* [shapeless](https://github.com/milessabin/shapeless)
* [Monocle](https://github.com/julien-truffaut/Monocle)
* [Spire](https://github.com/non/spire)
* [Many typelevel projects...](https://typelevel.github.io/projects/)

---

__P.S:__ This repo is successful due to the wonderful [contributions](https://github.com/hemanth/functional-programming-jargon/graphs/contributors)!
