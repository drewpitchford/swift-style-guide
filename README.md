![Backstage Swift Style Guide](images/backstage.png)

# Backstage's Swift Style Guide

This style guide is written primarily with the development of iOS and OS X applications using the Xcode IDE in mind.  As such, many of the guidelines found within are based around consistency with Apple's frameworks & Xcode's default preferences.  If you are developing with Swift using different frameworks, or a different IDE, you may find sections of this style guide to be less applicable.

## Table of Contents

* [General](#general)
  * [Whitespace](#whitespace)
  * [Braces](#braces)
  * [Control Flow](#control-flow)
  * [Loops](#loops)
* [Classes and Structures](#classes-and-structures)
  * [Avoid Explicit Use of Self](#avoid-explicit-use-of-self-except-where-required)
* [Closures](#closures)
* [Types](#types)
  * [Constants](#constants)
  * [Optionals](#optionals)
  * [Type Casting](#type-casting)
  * [Struct Initializers](#struct-initializers)
  * [Shorthand](#shorthand)
  * [Typealiasing](#typealiasing)
* [Language](#language)
* [Swiftlint](#swiftlint)
* [Credits](#credits)

## General

### Whitespace

Code should be indented four spaces per indentation level.  Do not insert tab characters.

Files should end with a new line.

Calls to `super` should be followed by an empty line.

The closing brace for `guard` statements should be followed by an empty line.

---

### Braces

##### Opening braces should be placed on the same line as the declaration they are encapsulating.

*Preferred*
```swift 
if someCondition {
    // execute some code
}
```

*Not Preferred*
```swift
if someCondition 
{
    // execute some code 
}
```

Closing braces should always be on a new line by themselves, horizontally aligned with the left edge of the opening brace it is closing.

*Rationale: Apple uses same line brackets in their code and Xcode autocomplete encourages it as well.*

---

### Control Flow


#### Put each conditional logic statement on a new line for control flow statements

*Preferred:*

```
if condition1,
   condition2 {
   // execute some code
}
```

*Not Preferred:*

```
if condition1, condition2 {
    // execute some code
}
```

##### Omit unnecessary parenthesis around control flow statements.

*Preferred:*

```swift
while someCondition {
    // execute some code
}
```

*Not Preferred:*

```swift
while (someCondition) {
    // execute some code
}
```

*Rationale: With braces required around bodies, the conditional part is perfectly clear without parenthesis and reads better.*

---

##### Prefer to `return` and `break` early.   

*Preferred:*

```swift
guard shouldDoTheThing else {
    return
}

// Do the thing.
```

*Not Preferred:*

```swift
if shouldDoTheThing {
    // Do the thing.  
}
else {
    return
}
```

*Rationale: This eliminates unnecessary nesting and makes the exit conditions clear up front.*

---

##### When using an early `return` or `break`, prefer `guard` to `if` statements.

*Preferred:*

```swift
guard shouldDoTheThing else {
    return
}
```

*Not Preferred:*

```swift
if !shouldDoTheThing {
    return  
}
```

*Rationale: The `guard` statement guarantees the early exit.  If the scope isn't exited, it will generate compile-time errors.  It is also easier for readers to identify as an early exit.*

---

##### Prefer using a `switch` statement over `else if` chains when dealing with enumerations.  

*Preferred:*

```swift
switch someValue {
case .foo:
    doFooThing()
case .bar:
    doBarThing()
}
```

*Not Preferred:*

```swift
if someValue == MyEnum.foo {
    doFooThing()
}
else if someValue == MyEnum.bar {
    doBarThing()
}
```

*Rationale: With a `switch` statement, we can more clearly distinguish whether all cases are handled.  It is also more compact.*

---

##### Prefer switching on tuples than unnecessary levels of nesting.

*Preferred:*

```swift
switch (someValue, direction) {
case (.foo, .left):
    doLeftThing()
case (.foo, .right):
    doRightThing()
case (.bar, .left):
    doBarThing()
case (.bar, .right):
    break       
}
```

*Not Preferred:*

```swift
switch someValue {
case .foo:
    switch direction {
    case .left:
        doLeftThing()
    case .right:
        doRightThing()
    }
case .bar:
    if direction == .left {
        doBarThing()
    }
}
```

*Rationale: Switching on a tuple makes all of the possible conditions more clear from the start.  It also becomes more compact and removes unnecessary levels of nesting.*

---

##### Prefer starting `else` and `catch`  statements on a new line, under the closing brace for the previous statement.

*Preferred:*

```swift
do {
    try doTheThing()
}
catch let error {
    handle(error)
}
```

*Not Preferred:*

```swift
do {
    try doTheThing()
} catch let error {
    handle(error)
}
```

*Rationale: Putting these statements at the beginning of a new line helps find all of the associated statements.  It also looks significantly better when collapsing code blocks in Xcode.*

![Uncuddled](images/uncuddled.png)

![Cuddled](images/cuddled.png)

---

### Loops

##### Prefer `for`-`in` loops to `forEach` in most circumstances.

*Preferred:*

```swift
for thing in things {
    thing.doTheThing()
}
```

*Not Preferred:*

```swift
things.forEach { thing in
    thing.doTheThing()
}
```

*Rationale: The preferred style reads more naturally.*

---

##### When dealing with optional collections, prefer `forEach` to unwrapping.

*Preferred:*

```swift
things?.forEach {
    thing.doTheThing()
}
```

*Not Preferred:*

```swift
if let things = things {
    for thing in things {
        thing.doTheThing()
    }
}
```

*Rationale: While the `for`-`in` loop reads more naturally, using `forEach` to prevent unnecessary levels of nesting helps keep the overall code more readable.*

---

##### When the loop body is nothing but passing each item in the loop into a closure, function, or method, prefer `forEach`.

*Preferred:*

```swift
things.forEach(handleTheThing)
```
*Not Preferred:*

```swift
for thing in things {
    handleTheThing(thing)
}
```

*Rationale: This is more compact, and passing closure arguments into methods that expect closures should feel perfectly natural in Swift.*

---

##### Prefer iterating over an array slice to any other sort of logic to deal with a specific section of an array.

*Preferred:*

```swift
for thing in things[first...last] {
    thing.doTheThing()
    handleTheThing(thing)
}
```

*Not Preferred:*

```swift
for index in first.stride(through: last, by: 1)
    let thing = things[index]
    thing.doTheThing()
    handleTheThing(thing)
}
```

*Rationale: In almost all cases, iterating over the array slice will be both more compact source code and more efficient.*

---

##### Prefer not to pull items out of an array index within a loop.  If the `index` is needed, use the `enumerated()` method.

*Preferred:*

```swift
for (index, thing) in things.enumerated() {
    print("Found \(thing) at index \(index)")
}
```

*Not Preferred:*

```swift
for index in 0..<things.count {
    print("Found \(things[index]) at index \(index)")
}
```

## Classes and Structures

When deciding between using a class or a struct, it is important to keep in mind that structs are value types and classes are reference types.  We need to understand the implications of using a value type versus a reference type when deciding what type to use.  

As a general rule, using structs will be safer when passing the data to multiple sources or when dealing with a multithreaded environment.  Because structs are a value type, their value is copied, so they cannot be mutated outside of the scope they are used in.  With structs, there is far less need to worry about memory leaks or race conditions.

However, because structs are value types, there is a performance hit when passing large structs around.  The larger the struct, the more data needs to be copied when we pass it.  Because classes are reference types, we are only passing a pointer to that class, so the performance is the same when passing classes, no matter the size of the class.  

However, we should be careful not to default to choosing classes for this reason alone.  The memory issues and potential for race conditions that we have to deal with and protect against with classes is arguably harder to debug and resolve.  And we shouldn't optimize for performance until performance has been measured and proven to be a problem.

It is often best to default to structs, only falling back to classes when you specifically require the functionality they provide (i.e. multiple references to the same object). This is due to struct's overall greater safety and efficiency. However, it is also important to understand the use cases for both.  While structs and classes are overall very similar, structs are more-so designed to encapsulate a small amount of values while classes are well-suited to represent custom data constructs and when you wish to represent or link to an external resource (such as files or services.)  And keep in mind with protocols & protocol extensions, we can deal with a lot of inheritance semantics even with structs.

---

##### Avoid explicit use of `self` except where required.

*Preferred:*

```swift
func setUpUI() {
    view.backgroundColor = UIColor.blue()
}
```

*Not Preferred:*

```swift
func setUpUI() {
    self.view.backgroundColor = UIColor.blue()
}
```

*Rationale: Omitting `self` allows for more concise code.  Only use it when a local variable has hidden an instance variable or in escaping closures.*

---

## Closures

##### When calling a method with a single closure as the last argument, leave it outside the parenthesis.

*Preferred:*

```swift
UIView.animate(withDuration: animationDuration) {
    self.myView.alpha = 0
}
```

*Not Preferred:*

```swift
UIView.animate(withDuration: animationDuration, animations: {
    self.myView.alpha = 0
})
```

*Rationale: By leaving the closure out of the parenthesis, it makes the code cleaner.  This style also allows the creation of functions that look & feel like extensions of the Swift language itself.*

---

##### When calling a method with multiple closure arguments, include all of the closures within the parenthesis.

*Preferred:*

```swift
UIView.animate(withDuration: animationDuration, animations: {
        self.myView.alpha = 0
    },
    completion: { _ in
        self.myView.removeFromSuperview()
    }
)
```

*Not Preferred:*

```swift
UIView.animate(withDuration: animationDuration, animations: {
        self.myView.alpha = 0
    }) { _ in
        self.myView.removeFromSuperview()
}
```

*Rationale: The `}) {` syntax can be very confusing, and this makes the code slightly less readable compared to passing both closures in with their named arguments.  In general, we should simply prefer to avoid functions that take multiple closures, where possible.*

---

##### For single expression arguments, use implicit returns if the context is clear.

*Preferred:*

```swift
let filteredRestaurants = restaurants.filter { $0.id == searchID }
```

*Not Preferred:*

```swift
let filteredRestaurants = restaurants.filter { restaurant in
    return restaurant.id == searchID
}
```

*Rationale: When the context is clear, the implicit return allows more compact code without sacrificing any readability.*

## Types

##### Let Swift implicitly decide which type to use except when you require a type different from Swift's default inference.

*Preferred:*

```swift
let count = 100
let width: CGFloat = 80.0
let name = person.name
```

*Not Preferred:*

```swift
let count: Int = 100
let width: CGFloat = 80.0
let name: String = person.name
```

*Rationale: Swift type inference system is well documented.  Adding types where unnecessary adds clutter to the source code.*

---

##### Prefer Swift native types to Objective-C's types.

*Preferred:*

```swift
var people: [Person] = []
```

*Not Preferred:*

```swift
var people = NSMutableArray()
```

*Rationale: In the case of collections, preferring Swift-native types allows for more type-safety, one of the biggest advantages Swift has over Objective-C.  In the case of other types, Swift is optimized for its native types.  Almost (if not all) of the behavior is bridged across both types, and whenever you absolutely must have the Objective-C type, you can freely cast into it.*

---

### Constants

##### Prefer constants to variables.  Use `let` as your default declaration keyword, and only change to `var` when you know the value of a variable will change.

*Preferred:*

```swift
let minimumPasswordLength: Int
```

*Not Preferred:*

```swift
var minimumPasswordLength: Int
```

*Rationale: Using `let` for variables which should not change can prevent bugs and keep the code's intent more clear.*

---

### Optionals

##### Avoid implicitly unwrapped optionals.

*Preferred:*

```swift
var profilePicture: UIImage?
```

*Not Preferred:*

```swift
var profilePicture: UIImage!
```

*Rationale: Implicitly unwrapped optionals can lead to [all sorts of problems](https://metova.com/blog/dev/problem-implicitly-unwrapped-optionals/) and crashes.  A little bit of extra unwrapping code is worth knowing that you won't see crashes from unwrapping `nil`.*

---

##### Do not force unwrap optionals.  If crashing *is* the correct behavior when the optional is `nil`, prefer a `fatalError` with a message.

*Preferred:*

```swift
guard let unwrapped = someOptional else {
    fatalError("Some helpful debugging message describing the circumstance.")
}
```

*Not Preferred:*

```swift
let unwrapped = someOptional!
```

*Rationale: An extra line of code here can save plenty of headaches when debugging.*

---

##### If a value should never be `nil`, prefer a non-optional.

*Preferred:*

```swift
class Person {
    let name: String
}
```

*Not Preferred:*

```swift
class Person {
    // name should never be nil
    var name: String?
}
```

*Rationale: Using optionals for values that really never should be `nil` makes code unclear.  It can lead to bugs or crashes, and it makes it harder for other devs to understand your code.  Also, using optionals has an added unwrapping overhead.  Using optionals for values that should never be `nil` is usually a sign of problems elsewhere in the code.*

---

##### When you need to unwrap an optional into a non-constant, prefer `if var` and `guard var`.

*Preferred:*

```swift
if var volume = volume {
    // use mutable volume
}
```

*Not Preferred:*

```swift
if let volume = volume {
    var vol = volume
    // use mutable vol
}
```

*Rationale: Using `if var` and `guard var` prevents muddying the current scope with unnecessary variable declarations.*

---

##### When you need to unwrap a variable from a higher scope, prefer simply shadowing the variable name.

*Preferred:*

```swift
var imageURL: URL?

// later...
guard let imageURL = imageURL else { /*...*/ }
```

*Not Preferred:*
```swift
var imageURL: URL?

// later...
guard let unwrappedImageURL = imageURL else { /*...*/ }
```

*Rationale: This prevents cluttering the current scope with more any more variables than we actually need, and makes the code slightly easier to read.*

---

##### When unwrapping multiple variables, prefer the same `if let` or `guard let` clause.

*Preferred:*

```swift
if let person = people.first, let profileImageURL = person.profileImageURL {
    // do something with profileImageURL
}
```

*Not Preferred:*

```swift
if let person = people.first {
    if let profileImageURL = person.profileImageURL {
        // do something with profileImageURL
    }
}
```

*Rationale: This prevents unnecessary levels of nesting and helps avoid "arrow code".*

---

### Type Casting

##### Do not force cast. If crashing *is* the correct behavior when a type cast fails, prefer a `fatalError` with a message.

*Preferred:*

```swift
guard let firstName = json["first_name"] as? String else {
    fatalError("Some helpful debugging message describing the circumstance.")
}
```

*Not Preferred:*

```swift
let firstName = json["first_name"] as! String
```

*Rationale: An extra line of code here can save plenty of headaches when debugging.*

---

### Struct Initializers

##### Prefer native Swift initializers over the legacy C "make" functions.

*Preferred:*

```swift
let bounds = CGRect(x: 0, y: 0, width: 100, height: 80)
let center = CGPoint(x: 50, y: 50)
```

*Not Preferred:*

```swift
let bounds = CGRectMake(0, 0, 100, 80)
let center = CGPointMake(50, 50)
```

*Rationale: The named parameters of the Swift initializers make it clear which argument is which, making the code more readable.  This will also be consistent with how we initialize all of our other custom structs.*

---

### Shorthand

##### Prefer more concise type declarations.

*Preferred:*

```swift
var people = [Person]()
```

*Not Preferred:*

```swift
var people: Array<Person> = Array<Person>()
```

*Rationale: The more compact version is easier to read, and no information is lost.*

---

### Typealiasing

##### Use typealiases to make your code more clear.  This is especially true when dealing with units of measure.

*Preferred:*

```swift
typealias Miles = Double

func travel(distance: Miles) {}
```

*Not Preferred:*
```swift
func travel(distance: Double) {}
```

*Rationale: Using typealiases makes our code self-documenting.  In the above example, without the `typealias`, it could be hard to figure out whether we're passing in the correct unit of measure, or whether we need to do a conversion before calling the method.  With the `typealias`, it is made clear that we must pass `distance` in as a measure of `Miles`.*

## Language

##### Code should be written in US English.

*Preferred:*

```swift
var color: UIColor
```

*Not Preferred:*

```swift
var colour: UIColor
```

*Rationale: For consistency with the existing frameworks we use in iOS development, we should use the American spellings.*

## Swiftlint

Backstage uses [swiftlint](https://github.com/realm/SwiftLint) to enforce as much of this style guide as possible across all Swift projects.  This repository also contains [the swiftlint configuration](.swiftlint.yml) file Backstage uses for its projects.  

## Credits

This style guide was forked from the [Metova](https://metova.com) style guide repo on Github.

* [Nick Griffith](https://github.com/nhgrif) - Original author of this document
* [Logan Gauthier](https://github.com/lgauthier) - Primary maintainer of Metova's original Swift style guide
* [Chris Dillard](https://github.com/cdillard) - Primary SwiftLint resource for Metova
* [Kyle Bashour](https://github.com/kylebshr) - Primary author of Metova's original Swift style guide

You can find [the Metova Team on Stack Overflow](http://stackoverflow.com/teams/68/metova).
