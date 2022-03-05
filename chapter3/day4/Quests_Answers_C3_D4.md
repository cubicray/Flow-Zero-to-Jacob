## Quests Answers - Chapter 3, Day 4

1. Explain, in your own words, the 2 things resource interfaces can be used for (we went over both in today's content)
> 1. Interfaces are used to specify certain requirements for resources and structs. When we specify an interface, we are requiring the resource/struct to implement the things specified in the interface.
> 2. Also, when we restrict a resource/struct to a specific interface, we can control what things this resource/struct has access to. The resource/struct will only have access to what is defined in the interface.

2. Define your own contract. Make your own resource interface and a resource that implements the interface. Create 2 functions. In the 1st function, show an example of not restricting the type of the resource and accessing its content. In the 2nd function, show an example of restricting the type of the resource and NOT being able to access its content.

```swift
pub contract MyContract {
  
  pub resource interface INFT {
    pub var id: Int // we only expose the id variable
  }

  pub resource NFT: INFT {
    pub var id: Int
    pub var project: String

    init() {
      self.id = 1512
      self.project = "Hoodlums"
    }
  }

  pub fun createNFT() {
    let NFT: @NFT <- create NFT()

    log(NFT.id) // 1512
    log(NFT.project) // "Hoodlums"

    destroy NFT
  }

  pub fun createINFT() {
    let NFT: @NFT{INFT} <- create NFT() // restricted to the INFT interface

    log(NFT.id) // 1512
    log(NFT.project) // ERROR: member of restricted type is not accessible: project
      
    destroy NFT
  }
}
```

3. How would we fix this code? 

```swift
pub contract Stuff {

    pub struct interface ITest {
      pub var greeting: String
      pub var favouriteFruit: String
    }

    // ERROR:
    // `structure Stuff.Test does not conform 
    // to structure interface Stuff.ITest`
    pub struct Test: ITest {
      pub var greeting: String

      pub fun changeGreeting(newGreeting: String): String {
        self.greeting = newGreeting
        return self.greeting // returns the new greeting
      }

      init() {
        self.greeting = "Hello!"
      }
    }

    pub fun fixThis() {
      let test: Test{ITest} = Test()
      let newGreeting = test.changeGreeting(newGreeting: "Bonjour!") // ERROR HERE: `member of restricted type is not accessible: changeGreeting`
      log(newGreeting)
    }
}
```
> Fix:
> 
```swift
pub contract Stuff {

    pub struct interface ITest {
      pub var greeting: String
      pub var favouriteFruit: String
      pub fun changeGreeting(newGreeting: String): String // add function definition to expose function 
    }

    pub struct Test: ITest {
      pub var greeting: String
      pub var favouriteFruit: String // define favouriteFruit variable to conform to interface

      pub fun changeGreeting(newGreeting: String): String {
        self.greeting = newGreeting
        return self.greeting
      }

      init() {
        self.greeting = "Hello!"
        self.favouriteFruit = "Apple" // initialization
      }
    }

    pub fun fixThis() {
      let test: Test{ITest} = Test()
      let newGreeting = test.changeGreeting(newGreeting: "Bonjour!")
      log(newGreeting)
    }
}
```
