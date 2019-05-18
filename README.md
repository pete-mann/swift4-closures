# Swift 4 Closures & Higher Order Functions
## Introduction
I have been learning Swift 4 for about 10 weeks, it is one of the most enjoyable languages I have learnt recently. The 
most exciting thing about learning Swift are optionals, type inference, guard statements and lastly `Function Type` 
which makes it really easy to pass functions and closures around. 

I enjoyed learning optionals and I could talk about them all day, but this article is about functional programming in 
Swift 4, so let's get busy. 
## Pre-requisites
This interactive guide is intended for readers with intermediate to advanced programming knowledge. Because this guide 
will progress into passing closures as parameters and using higher order functions, this is a segue to functional 
programming using Swift 4. It's important to have at least basic knowledge of programming in Swift in order to 
understand these concepts. 

You may want to skip straight to the examples, that is fine. However the information before the examples is important, 
some of the topics that I'll cover first are considered more advanced than the examples.

Why are there no comments? Personally I find lots of comments within the code make it difficult to read. So I have left 
the comments to the paragraphs within this guide.
## Closures
A Closure is a block of code, the term block being of programming nomenclature that means source code grouped together. 
In Swift 4 the block of code is wrapped in curly braces `{}`. The term Closure is similar to the term block in C or 
Lambda in other programming languages such as Java.
## Closure Scope
Closure scope is captured from the context at the time of declaration. So even though closures can be passed around a 
Swift application in parameters, the closure still has access to the variables in the scope of which the closure is 
defined.

Since we get a closure from the `SwiftClosure` class and use it in the `SwiftScope` class what will the closures scope 
be? To answer that question, let's look at the following example. This may be a rather advanced example, since we 
haven't even got to basic higher order functions, but keep this in mind as it is really important if you want to pass 
closures. Getting this wrong can be challenging to debug.

In the following example there are two classes `SwiftClosure` and `SwiftScope`. To clarify things, I'm going to list out 
their roles and responsibilities.

#### SwiftScope
* Stores a reference to the `SwiftClosure` class
* Contains a private array of `names`, these names will be accessed using the closure
* Contains the `run()` method, which is used to start the example and get the print message 
* Purpose of this class is to call the `forEach` method on the `names` array, passing in a closure.
#### SwiftClosure
* Contains the closure, the closure is declared in this class. Therefore it has access to the `SwiftClosure` class 
using `self`.
* Contains a private array of `scopedTypes`, these are types of the character names that are in the `SwiftScope` class.
* Purpose of the class is to provide a closure that can print both `names` and `scopedTypes`
#### Explanation
The `run()` method gets a closure from the `SwiftClosure` class. Now this closure has access to the members of the 
`SwiftClosure` class, as you can see these are being printed to the output from this line 
`self.scopedTypes[tuple.offset]`. So because the closure is defined in the `SwiftClosure` class it can access the scope 
of that class. Again this is really important to understand especially when the closure method may be many lines long. 
```
class SwiftScope {
    
    private let swiftClosure: SwiftClosure
    
    let names = ["Pete", "Chewbacca", "George", "Anakin", "Rai", "Darth Vader", "Kylo"]
    
    init(swiftClosure: SwiftClosure) {
        self.swiftClosure = swiftClosure
    }
    
    func run() {
        names.enumerated().forEach(self.swiftClosure.getClosure())
    }
    
}

class SwiftClosure {
    
    private let scopedTypes = ["Fan", "Wookie", "Film maker", "Aprentice Jedi", "Jedi", "Dark Lord", "Dark Warrior"]
    
    func getClosure() -> ((offset: Int, element: String)) -> Void {
        return { (tuple) -> Void in
            print(tuple.element, self.scopedTypes[tuple.offset])
        }
    }
    
}

let swiftScope = SwiftScope(swiftClosure: SwiftClosure())

swiftScope.run()
```
Output:
```
Pete Fan
Chewbacca Wookie
George Film maker
Anakin Aprentice Jedi
Rai Jedi
Darth Vader Dark Lord
Kylo Dark Warrior
```
## Type Signatures
Functions and closures both adhere to the same type in Swift 4. They are therefore of the same type and this type in  
Swift 4 is called [Function Type](https://docs.swift.org/swift-book/ReferenceManual/Types.html#ID449). The exact type 
of `Function Type` depends on the signature type of the function or closure being declared. This is important to 
understand because different function signatures will have different `Function Types` and will not be compatible. 
We can therefore write the following code to declare a variable with a Function (or closure) type, the semantics of the 
type indicate that the `Function Type` has no parameters and returns nothing. Therefore it is possible to assign a 
function or closure of the same signature `Function Type` to this variable in the future.
```
var closure: () -> Void
```
Is the same as 
```
var closure: () -> ()
```
The reason the second example works is because in Swift `()` is the same as `Void`. This is true because in Swift, 
`Void` is actually an empty tuple. If you don't know what a tuple is, [A tuple type is a comma-separated list of types, 
enclosed in parentheses.](https://docs.swift.org/swift-book/ReferenceManual/Types.html#ID448) Any more explanation is 
out of the scope of this article. However it is important to understand because we will be using the `Tuple Type` in 
this guide.

Should you find that either of these examples creates an error in Swift Playgrounds, then the following example may 
help clarify why.
```
var closure: (() -> Void)?
```
Note that the actual type is wrapped in parenthesis with the question mark at the end so that we can simply illustrate 
a variable with a Function Type and a value of nil. This is of course because of Swifts optional. For the purpose of 
illustrating the same thing without the optional variable, here is the same closure declared inside a function. The 
reason it is optional is because you can create uninitialised variables in the root of the REPL. 
## Using `typealias`
Using `typealias` it is possible to define a type or function signature type. With this `typealias` defined it can then
be used to replace the more verbose definition of a closure by replacing the type with the `typealias`. Of course using
`typealias` is not required, and if you find that it is less readable then you don't have use it.
```
typealias Sorter = (String, String) throws -> Bool
```
Now when declaring a closure we can replace the type with the `typealias`, this improves readability.
```
let sortByLength: Sorter = { (l, r) -> Bool in
    return l.count < r.count
}
```
## Optional Parenthesis
Both of these examples will work and produce the same result. Note in the second example the `.sort()` parenthesis are 
missing, also note that the argument label `by:` is also missing. This is because the last parameter or in this case 
the only parameter is a Function Type, then the parenthesis are not required. Again this can be seen in the second 
example.

Let's create some names. Note that names is a `var`, this is because `.sort()` modifies the array, unlike `.sorted()` 
which returns a new sorted array and does not modify the original array.
Verbose sort example
```
names.sort(by: { (a: String, b: String) -> Bool in
    return a < b
})
```
Is the same as
```
names.sort { (a, b) in
    a < b
}
```
Is the same as 
```
names.sort { $0 < $1 }
```
And lastly sort can be called like this as well.
```
names.sort(by: <)
```
## Implied Types
Let's again look at the previous example. All of the examples are the same, however we did not mention in the previous 
section that the second of these examples does not include the argument type of `String` for each argument, nor does it 
include the return type of `Bool`. This is because of the usage of implied types in the first example. Both argument 
and return types are implied.
## Implicit Return
Again look at the previous example, the second example does not include a `return` keyword. That is because when there 
is only one line in the closure body the `return` keyword is optional.
## Shorthand Arguments
Before we move on, let's again look at the third example of previous examples. There are no arguments `(a, b)`, why? 
Notice how the arguments `a` and `b` are replaced with `$0` and `$1`. The omission of the arguments is known as 
shorthand arguments, note that the order of arguments is represented by the number it is assigned, eg. `a` = `$0`.
## Higher Order Functions
Higher order functions are functions that accept a function or closure as a parameter and, sometimes return a function 
to the caller. See the examples section for a list of functions that will be covered.

If this hurts your brain at this point, keep reading because everything will become much clearer. We will work through 
some basic higher order examples that will prepare you for your journey to becoming more confident using functional 
programming in Swift 4.
## Examples
This document will attempt to provide some very basic examples of some of the common Swift 4 methods of the 
[Array Type](https://docs.swift.org/swift-book/ReferenceManual/Types.html#ID450). Throughout this document I may be 
using implied argument and return types, as this results in less code. Writing less code should not be the main purpose
behind using implied types, that is because less code does not equal code that is easy to understand. This might be a 
preference of yours or a particular style of code that is required in your project. 

This is by no means a comprehensive list of the higher order functions that can be called on `Array`, these are just 
some of the most used in my experience. 

Throughout these examples, I will be using the parameter name of `element`. Remember since we are using the 
`names` array, the `element` parameter is always referring to a string. Therefore we can perform `String` functions on 
the `element` parameter. I like to use the parameter name of `element` when looping over an array, since it is indeed 
an element of an array. Using parameter names like `name` in this instance would also work, however I use the 
convention of `element` because often the `element` parameter will be an `Object`, not a `String`.

The document will also provide some more advanced implementations within the examples. We will be looking at these 
methods:
* `Array.sort()`
* `Array.sorted()`
* `Array.forEach()`
* `Array.map()`
* `Array.reduce()`
* `Array.filter()`
* `Array.compactMap()`
* `Array.first()`
* `Array.firstIndex()`

## Sorted
Using the `.sorted()` method of array we can sort an array using a closure and chain another closure afterwards. This is 
called method chaining, this is possible because `.sorted()` returns an array that we can call `.sorted()` on again.
```
var sorted = names.sorted(by: <).sorted(by: sortByLength)
```
Wait. What is the reference to `sortByLength`? Remember that we discussed `typealias` earlier? Well we actually created
a `softByLength` closure that we can apply here. That code again is:
```
let sortByLength: Sorter = { (l, r) -> Bool in
    return l.count < r.count
}
```
## Foreach
The `.forEach()` method is used here to iterate over an array. The `forEach` method iterates over a copy of the array.
```
names.forEach { (element) in
    print(element)
}
```
It's also worth mentioning that the `.enumerated()` method of array can be used to access the index _and_ element of an
array, which I have found is often far more useful than simply accessing the element as aforementioned. The Swift 4 
documentation states that `.enumerated()` "Returns a sequence of pairs (*n*, *x*), where *n* represents a consecutive 
integer starting at zero and *x* represents an element of the sequence." Remember tuples? A 
**sequence of pairs (*n*, *x*)** is a tuple! Therefore we can write the following code, again omitting the parenthesis 
for the .`forEach()` method, and here the closure will receive the sequence of pairs (tuple).  
```
names.enumerated().forEach { (tuple) in
    print(tuple.offset, tuple.element)
}
```
## Map
Map is used to convert the contents of an array, the term map means to map it to something else. Map returns a new copy 
of the array. It does not modify the original array. The closure returns an `Int` because `element.count` _is_ and 
`Int`. If we were returning a `String`, we should obviously change the return to `String`.
```
names.map { (element) -> Int in
    element.count
}
```
This code will produce an array of `Int` where each element is the number of characters in each name.
## Reduce
Reduce is used to reduce the array to one element. Reduce can be more difficult to understand than `.filter()` and 
`.map()`, however I find that using the following parameter names of `accumulator`, and `current` (from the JavaScript 
Mozilla Development Network (MDN)) really helps to understand what is going on inside the `reduce()` operation. The 
empty string inside the `reduce("")` method call is the initialised value for the `accumulator` parameter, this must be
the same type as the closure return type, ie the closure will eventually return the `accumulator` at then end of the 
operation.
```
names.reduce("") { (accumulator: String, current: String) -> String in
    accumulator + "\(current.uppercased()), "
}
```
Will produce a string of uppercase names separated by a comma.
## Filter
The `.filter()` method is used to filter an array. Moreover, by returning `true` the output of this operation will 
include the value of the current `element`, in this instance, all elements containing a lowercase **e** will be 
included in the final result. Because the `Function Type` passed into the .`filter()` method has a return type of 
`Bool` it is impossible to return anything other than the value of the current element in the array while filtering. I 
hear you saying, what if I want to modify while filtering? I've got you covered, check out the `.compactMap()` method.
```
names.filter { (element) -> Bool in
    element.contains("e")
}
```
## CompactMap
Compact map is useful when trying to map and filter in the same operation, we could do this with `map()` and chain the 
`.filter()` method on the map result, however as you know this will cause the code to loop over the array twice.  

So if you ever need to filter and map, use `.compactMap()`. In the below example we are getting the initial of each 
name in the `names` array. Then we are returning a `Tuple Type` which includes the name matching the filter and the 
initial for that name.
```
names.compactMap { (element) -> (String, String)? in
    let initial = element.prefix(1).uppercased()
    return initial == "A" ? (initial, element) : nil
}
```
## First
The `.first()` method is something I use often. For example if I just want to test that something exists in an array, 
or I know that my array contains unique elements I may not care about fetching more than the first result. 
```
names.first { (element) in
    element.contains("e")
}
```
## FirstIndex
Often we will need to find the index of an element, this can not be done with `.first()` since first will return the 
element itself. 
```
names.firstIndex { (element) in
    element.contains("Chewbacca")
}
```
## Summary of Basic Higher Order Functions
If you're still reading, great. By now you should have a good understanding of the syntax of the `Function Type`. Armed
with this knowledge of the higher order functions available to you on `Array`.