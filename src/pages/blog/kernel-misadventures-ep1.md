---
layout: "../../layouts/BlogPostLayout.astro"
title: "kernel misadventures: episode 1"
summary: "i think i took \"build your own os\" too seriously..."
date: 2026-04-12
---

so i've recently gotten into low-level development. it's been really fun to learn about how computers actually *do* stuff! i now have opinions on weird things like calling conventions, assembly syntax, and kernel architecture. but one thing has not been so fun:

## languages

every languages has its own benefits and tradeoffs, and they *all* look appealing in different ways. pair that with an indecisive developer, and you get procrastination.

### c

i hope you expected to see this language mentioned at some point. it's the tried and true, "been there; done that" language. except it has one huge, glaring issue:

```c
int *ptr = NULL;  // ✅ make a pointer to nothing
do_literally_anything(*ptr)  ✅ // dereference that pointer
// 🧨 crashes because you just accessed nothingness
```

c has literally no safety at all. which is not good for kernel development unless you enjoy setting your computer on metaphorical fire.

### rust

rust is much better, because you can't HAVE null in the first place. the borrow checker will make sure all of your code is safe before it gets compiled. lets try it out with another example, using a use-after-free bug:

```rust
let x: &str = "let's get rusty"
let y = x  //  ✅ moves x into y, freeing x
let z = x  // 🚫 rust knows that x is already free, so this doesn't compile 
```

but rust does have its own issues too. like, it will fight you every single step of the way while setting up a kernel, and you'll end up writing complex build scripts and fighting with rust-analyzer to get the language server working. also, most rust examples assume that you have the standard library, although the **`#[no_std]`** marker will easily disable it.

also, one tiny thing: rust eventually starts to look like explicitly-typed spaghetti if you work on a project for long enough.

### embedded swift

here's one you probably didn't expect (unless you read my [last post](/blog/swift-6.3-awesomeness)). yes, you can build a kernel and other bare-metal projects using embedded swift. you just lose a lot of stuff:

```swift
// 🚫 no existential "any"
func doSomething(with argument: any MyProtocol) { /* ... */ }
// 🚫 no classes, ARC, or allocator
let myClassInstance = MyClass()
// 🚫 no fancy heap types like String and dynamic Array<T>
let name = "Anna"
let friendNames = ["Charlie", "Levi", "Jakob"]  // ✅ this is fine
friendNames.append("David")  // 🚫 not fine because of the heap
// 🚫 no objective-c (obviously)
@objc
struct MyValue { /* ... */ }
// 🚫 no foundation library or os library (you are the OS)
import Foundation
import FoundationEssentials
import Darwin
import WinSDK  // who even uses windows anymore
import Glibc
import Musl
import Android  // yes, regular swift 6.3 supports android
print("this code doesn't work")
// ✅ only import swift and built-in features
import Swift
let name: StaticString = "Anna"  // stack string
let friendNames = ["Charlie", "Levi", "Jacob"]  // stack array
```

very fun. but, you still get swift's humane syntax and other great features of the language. and swift has near-perfect c interop (as long as everything used is marked **`@c`**):

```swift
// c types:
// - hartID: unsigned int
// - flattenedDeviceTreePointer: uint8_t
@c  // <-- magic annotation
func embeddedEntry(
    hartID: UInt,
    flattendDeviceTreePointer: UnsafePointer<UInt8>
) -> Never {
    /* perform entry and never return */
}
```

## so what's the best one?

sadly, there is no real answer to that. if you want to start a project, choose the language that fulfills your needs the most. right now i'm using rust, but i am looking at embedded swift.

thanks for reading my rambling! happy hacking.
