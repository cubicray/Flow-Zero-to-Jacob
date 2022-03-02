## Quests Answers - Chapter 3, Day 1

1. In words, list 3 reasons why structs are different from resources.
> 1. Resources cannot be copied, structs can.
> 2. Resources cannot be overwritten, structs can.
> 3. Resources cannot be created whenever you want, structs can.

2. Describe a situation where a resource might be better to use than a struct.
> A situation where a resource is better to use is when dealing with sensitive data such as NFTs. NFTs are unique digital assets that can be very valuable. Using resources to handle NFTs is much safer because developers are forced to be explicit about the operations they want to perform on those NFT resources. With resources, it is almost impossible to lose an NFT unless the developer explicitly deletes the NFT resource using the `destroy` keyword.

3. What is the keyword to make a new resource?
> `create`

4. Can a resource be created in a script or transaction (assuming there isn't a public function to create one)?
> No, a resource can only be created inside a contract.

5. What is the type of the resource below?

```swift
pub resource Jacob {

}
```
> @Jacob

6. Let's play the "I Spy" game from when we were kids. I Spy 4 things wrong with this code. Please fix them.

```swift
pub contract Test {

    // Hint: There's nothing wrong here ;)
    pub resource Jacob {
        pub let rocks: Bool
        init() {
            self.rocks = true
        }
    }

    pub fun createJacob(): Jacob { // there is 1 here
        let myJacob = Jacob() // there are 2 here
        return myJacob // there is 1 here
    }
}
```
```swift
pub contract Test {

    pub resource Jacob {
        pub let rocks: Bool
        init() {
            self.rocks = true
        }
    }

    pub fun createJacob(): @Jacob { //we have to use the `@` symbol in front of a resource's type
        let myJacob <- create Jacob() // we can only make a new resource with the `create` keyword and we assign the new resource to `myJacob` using the `<-` "move" operator
        return <- myJacob // we use the `<-` symbol again to move the resource to the return value
    }
}
```
