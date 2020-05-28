---
layout: single
title: 'Swift tip: final keyword'
<!-- date: 2020-05-28 11:00:00 +0100 -->
categories:
  - Tips
  - Swift
tags:
  - keyword
  - inheritance
  - performance
---

The `final` *keyword* has both compile and runtime effects in a Swift (or mixed) codebase. Let's quickly check them.

### `Inheritance and code design`

[Inheritance](https://docs.swift.org/swift-book/LanguageGuide/Inheritance.html) is a common pattern to achieve [Polymorphism](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)). It has its use cases, Apple itself makes good use of it in the SDKs it provides us (*Foundation* and *UIKit* among others), which doesn't mean using it all around is a good idea.

That's where `final` comes in handy. 
As an example, a UIViewController declared final cannot be subclassed, it gets protected by the compiler, if anyone tries to do so, there will be a compile-time error.

The `intent and design choice` gets clear.

```swift
// This will make it impossible to override this class, even inside the same module
final class SomeViewController: UIViewController { }
```

Why UIViewController as an example 👀? Because I've seen base view controllers tons of times in the codebases I've touched throughout my career, and I strongly believe there are better techniques to reuse, compose and deal with view functionality than through a strong candidate for a [god object](https://en.wikipedia.org/wiki/God_object).

*Methods, properties, and subscripts* can also be marked as `final` by adding the keyword before their declaration.

```swift
class Vehicle {
   final var speed: Int = 0

   func accelerate() {
      speed += 10
   } 
}
```

### `When performance matters`

In case you work on a performance sensitive project or feature, the `final` keyword might interest you even more.

> "Use final when you know that a declaration does not need to be overridden."

It will optimize the compiler to directly call the functionality, reducing [`dynamic dispatch`](https://en.wikipedia.org/wiki/Dynamic_dispatch).

> "This means that the program has to determine at runtime which method or property is being referred to and then perform an indirect call or indirect access. This technique, called dynamic dispatch, increases language expressivity at the cost of a constant amount of runtime overhead for each indirect usage."

I recommend you to check Apple Developer's [`Increasing Performance by Reducing Dynamic Dispatch`](https://developer.apple.com/swift/blog/?id=27) in case you're curious about other ways to reduce dynamic dispatch and improve your code performance.

#### `See you next time`
Thanks for reading 😄. <br>
I'm working on a series of longer posts about how to create your own *HTTP networking*, with a lightweight but easy to deal and well-tested API. It might come out soon, stay tuned!

### References: 
* Apple Developer: [Inheritance](https://docs.swift.org/swift-book/LanguageGuide/Inheritance.html)
* Apple Developer: [Increasing Performance by Reducing Dynamic Dispatch](https://developer.apple.com/swift/blog/?id=27)