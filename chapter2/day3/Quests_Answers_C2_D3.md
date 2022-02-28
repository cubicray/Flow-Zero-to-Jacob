## Quests Answers - Chapter 2, Day 3

1. In a script, initialize an array (that has length == 3) of your favourite people, represented as `String`s, and `log` it.

> <img src="/chapter2/day3/screenshot_01_C2_D3.png" alt="screenshot" size="400">

2. In a script, initialize a dictionary that maps the `String`s Facebook, Instagram, Twitter, YouTube, Reddit, and LinkedIn to a `UInt64` that represents the order in which you use them from most to least. For example, YouTube --> 1, Reddit --> 2, etc. If you've never used one before, map it to 0!

> <img src="/chapter2/day3/screenshot_02_C2_D3.png" alt="screenshot" size="400">

3. Explain what the force unwrap operator `!` does, with an example different from the one I showed you (you can just change the type).

> The force-unwrap operator `!` is used with `optional types` denoted with a `?` after the type. `Optional types` can either be the actual type specified or `nil`. For example, if a variable is `Int?`, it means this variable can either be an integer or `nil`.

> If the optional is not `nil` it can be "unwrapped" using the force-unwrap operator `!`, meaning it will remove the `optional type` and return the actual type.
```swift
var number1: Int? = 7
var unwrappedNumber1: Int = number1! // this removes the optional type and returns the integer 7
```
> If the optional is `nil`, it cannot be unwrapped and attempting to do so will return an error (panic and abort).
```swift
var number2: Int? = nil
var unwrappedNumber2: Int = number2! // This returns an error because we are trying to force-unwrap nil which is not allowed
```

4. Using this picture below, explain...
    - What the error message means
    > The error message means that the output is expected to be of type `String` but instead it is an `optional type`.
    - Why we're getting this error
    > Values in a dictionary are returned as `optional types`.
    - How to fix it
    > We must add the force-unwrap operator `!` to return the actual value of a dictionary key:
    ```swift
    pub fun main(): String {
      let thing: {Address: String} = {0x01: "One", 0x02: "Two", 0x03: "Three"}
      return thing[0x03]! // added the force-unwrap operator
    }
    ```
 <img src="../chapter2/images/wrongcode.png" alt="drawing" size="400" />
