---
title: "Tail Recursion in Scala"
author: admin
layout: post
permalink: /2017/09/tail-recursion/
categories:
  - Scala
  - Functional Programming
tags:
  - fp
  - tail-recursion
  - scala
---

# Tail Recursion in Scala

## Motivation

I love learning new things, and this year might finally be the year of Functional Programming for me. I've worked on Scala on-and-off and have finally decided to dive right into the [`Functional Programming in Scala`](https://www.manning.com/books/functional-programming-in-scala) book. One of the things that caught my attention when I first started writing code in Scala was `Tail Recursion`. It took me a while to wrap my head around the concept, and found that all the material that I read just spoke about *what* tail recursive functions were, but not how to make your function tail recursive. Maybe I wasn't looking hard enough either.

When I finally got around to understanding it, it was simple enough. When I encountered it in the `FP in Scala` book again, I thought I'd write a quick post about it.

## Getting right to it

I'll explain the concepts of tail recursion by picking up a simple example: **Factorial of a number**

### How do we calculate the factorial of a number?

Let's break down what a factorial method does:

1. Take a number n
2. If the number is less than or equal to 1, the factorial of the number is 1
3. If the number is greater than 1, the factorial is equal to the number multiplied by the factorial of the number one less than itself.

### What are recursive functions?

You've heard this a few times short of a billion - Recursive functions are functions that call themselves. Let's see how the factorial of a number can be written as a recursive function (`BigInt`, cause this method generates big ints!):

```
  def factorial(n: BigInt): BigInt = {
    if (n <= 1)
      1
    else
      n * factorial(n - 1)
  }
```

The recursive method is a direct translation of the algorithm above, which can be represented mathematically as the series below:

```
factorial(n) = 1 * 2 * ... * n-1 * n
```

### Do I need recursive functions?

Not always. You can convert the above recursive factorial method into a non-recursive method that uses loops:

```
  def fac(n: BigInt): BigInt = {
    var i: BigInt = n
    var result: BigInt = 1
    while (i > 1) {
      result = result * i
      i -= 1
    }
    result
  }
```

This method uses `var`s. A more functional version can be found at the bottom of this blog.

Recursive functions are sometimes easier to reason about, and simpler to understand in code - if you can break your problem into sub-problems, and you see that the sub-problem is basically the problem itself with a different starting point, you can easily convert your solution into a recursive solution.

Recursion sounds great, what's the problem then?

### The problem with recursion

When you call a method in a language such as Scala or Java, the state of the system is stored in a `Stack`. When your method calls another method, the state of the current method is stored in the stack, and the called method gets control. When the called method complets, the old state is popped out of the stack and the caller's state is restored.
This can be made more clear here:

```
  val x = 10
  println(x)

  def someMethod() = {
    val x = 12
    println(x)
  }

  someMethod()
  println(x)
```

This prints:

```
10
12
10
```

The value of `x` is pushed onto the stack when `someMethod` is called, and then restored when `someMethod` completes. The value of `x` that `someMethod` sees is the value of `x` defined inside it.

When a recursive method is called, it basically stores its own state onto the stack before calling itself. Let's look at what the factorial method is doing:

```
factorial(10):
= 10 * factorial(9)
= 10 * (9 * factorial(8))
= 10 * (9 * (8 * factorial(7)))
...
```
As each `factorial` call calls the `factorial` for the previous integer, the number of layers in the call stack is equal to `n`. Stacks have limits, and like all sensible stacks, when you go over that limit, they throw a `StackOverflowError`. Your stack-trace (The `stack` in the stack trace is *exactly* this stack) looks something like this:

```
Exception in thread "main" java.lang.StackOverflowError
	at scala.math.BigInt$.int2bigInt(BigInt.scala:97)
	at com.caffinc.fp.Experiments$.factorial(Experiments.scala:11)
	at com.caffinc.fp.Experiments$.factorial(Experiments.scala:14)
	at com.caffinc.fp.Experiments$.factorial(Experiments.scala:14)
	at com.caffinc.fp.Experiments$.factorial(Experiments.scala:14)
	at com.caffinc.fp.Experiments$.factorial(Experiments.scala:14)
	...
```
A whole lot of the same line calling itself. Sometimes you might encounter stack overflows due to extremely complicated layering of methods calling other methods which call the first method, etc. (Think poorly written language parsers).
The above stacktrace can be reproduced by running the recursive version of `factorial` with an `n = 10000`. However, the iterative version that doesn't use recursive calls but instead uses vars works just fine. Should you sacrifice your cool recursive solution for one that uses `var`s? Nope. This is where `tail recursion` comes in.

### So what the heck are `tail recursive` functions?

A function is tail recursive if all the computation branches end either in values or a call to itself. Let's look at why our recursive `factorial` is not tail recursive:

```
  def factorial(n: BigInt): BigInt = {
    if (n <= 1)
      1 // Constant, looks fine
    else
      n * factorial(n - 1) // This branch actually needs to return back to this point to multiply n with the result of factorial(n - 1), so this branch is not tail-recursive
  }
```
So Bob, can we fix it? Yes we can! Let's look at a tail recursive version of `factorial`:

```
  def factorial(n: BigInt): BigInt = {
    def go(acc: BigInt, n: BigInt): BigInt = {
      if (n <= 1)
        acc
      else
        go(acc * n, n - 1)
    }
    go(1, n)
  }
```
Here, the inner `def go` is the replacement of the original `factorial` method. Look at the body of the method - the first branch is the value `acc`. The second branch is a call to itself which *does not have to return back to this point to do any further calculations*. So essentially the caller of `go` can fire-and-forget. The compile can now optimize this `tail call` into a `while loop` which eliminates the need for a stack to store the current state. If the compiler did not perform any optimization, the stack can still blow up, but you can verify that it indeed performs tail call optimization by calling this new `factorial` method:

```
  println(factorial(10000))
```
This prints:
If that isn't a `BigInt`, I don't know what is.

Your IDE might show you a hint that your method is tail call optimized:

![Tail Call](https://caffinc.github.io/images/tail-call.png)

### Conclusion

Tail calls are simple to do. If your method is recursive, try to use an accumulator to pass the results back into the methor itself so that all the branches of your recursive method either end in a call to itself or a value, without requiring any type of calculations after the recursive call.

I hope this was useful!

### A shorter, more functional `factorial`

Can we be more functional and use `foldLeft` or something to write the `factorial` method?

```
  def factorial(n: BigInt): BigInt = (BigInt(1) to n).foldLeft(BigInt(1))(_ * _)
```

Can I go smaller?

```
  def factorial(n: BigInt): BigInt = (BigInt(1) to n).product
```

The `foldLeft` and `product` methods are tail-recursion optimized already, so they solve the problem with recursion without leaking their detals to the caller.