---
layout: "../../layouts/BlogPostLayout.astro"
title: "babe, wake up, swift has module name selectors"
summary: "plus other stuff. it's like apple remembered what a language is!"
date: 2026-03-29
---

if you don't know, as of writing this, swift 6.3 has literally just released. it is very nice imo, as previously mentioned.

## @c is here!

previously, to expose something to c, you'd need to use the weird @\_cdecl attribute:

```swift
@_cdecl("greet")
func greet(_ name: StaticString) {
    // and then do some weird stuff to convert from a ptr to a staticstring and put it in `resolvedName`.
    /// ...
    print("Hello, \(resolvedName)!")
}
```

ew.

luckily, people noticed how weird this was. including me :3 but there were still some problems to be ironed out, such as making sure types could actually be represented in c.

so now we've got the newer, fancier, much cooler @c attribute!

```swift
@c
func greet(_ name: StaticString) {
    print("Hello, \(name)!")
}
```

it's so much cleaner already. oh, and it's waaay safer.

## module name selectors to make code more readable

my biggest pet peeve with swift libraries is when they have a type that has the exact same name as the module (looking at you, [drops](https://swiftpackageindex.com/omaralbeik/Drops)). like, no, i don't want to play _compiler roulette_ every time i build cookie clicker clone #2,643,891.

this snippet makes two of the exact same thing:

```swift
import Drops

Drops(/* ... */)  // implicitly from the drops module
Drops.Drops(/* ... */)  // explicitly from the drops module
```

that's just unnecessarily confusing. especially when you have multiple types with the same name.

now, it's much easier to specify exactly what type you are referring to:

```swift
import Drops

Drops::Drops(/* ... */)  // much more readable!
Drops(/* ... */)  // still a little confusing...
```

another place this could be useful is specifying a todo-like task v.s. a swift async task. say we're building an app named _Checkmark_:

```swift
let task = Checkmark::Task(self.title)  // a thing to do
Swift::Task {  // async code to execute
    self.upload(task)
}
```

it's just so much cleaner and easier to read in many situations. thanks, swift contributors!

## theres smaller stuff too

what would an update be without a billion smaller, helpful things to go along with it? here's a list of my favorites:

- you can use [swift build in swiftpm](https://docs.swift.org/swiftpm/documentation/packagemanagerdocs/swiftbuildpreview/) now
- docc got some new experimental features
  - exporting docs as markdown
  - seo and accessibility got improved because you can have small, static html summaries of each page
  - code blocks can be annotated to enable and disable features and styling
- embedded swift saw lots of small debugging improvements, and is starting to have a more usable toolset and dependency system
- [the swift sdk is on android](https://www.swift.org/documentation/articles/swift-sdk-for-android-getting-started.html)

if you're interested in swift, or c-inspired langauges, you should absolutely check out the [full article](https://www.swift.org/blog/swift-6.3-released/) on swift.org, especially the stuff on embedded swift. that's a little out of my pay grade.

thanks for reading my first post! it was fun to write.
