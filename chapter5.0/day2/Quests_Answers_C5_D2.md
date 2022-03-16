## Quests Answers - Chapter 5, Day 2

1. Explain why standards can be beneficial to the Flow ecosystem.
> Standards allow everyone to implement and adhere to specific requirements and conventions. They enable the development of consistent code, which increases efficiency and reduces complexity and errors. In addition, they improve code readability and maintenance. For example, the NonFungibleToken standard enables clients to develop universal solutions across all NFT contracts without having to customize a solution for each contract separately.

2. What is YOUR favourite food?
> Hot crepes with Nutella 😋

3. Please fix this code (Hint: There are two things wrong):

The contract interface:

```swift
pub contract interface ITest {
  pub var number: Int
  
  pub fun updateNumber(newNumber: Int) {
    pre {
      newNumber >= 0: "We don't like negative numbers for some reason. We're mean."
    }
    post {
      self.number == newNumber: "Didn't update the number to be the new number."
    }
  }

  pub resource interface IStuff {
    pub var favouriteActivity: String
  }

  pub resource Stuff: IStuff { // implement the resrouce interface
    pub var favouriteActivity: String
  }
}
```

The implementing contract:

```swift
import ITest from 0x??? // import the contract interface

pub contract Test: ITest { // implement the contract interface
  pub var number: Int
  
  pub fun updateNumber(newNumber: Int) {
    self.number = 5
  }
  
  // No need for this resource interface here (it stays in the contract interface).
  // pub resource interface IStuff {
  //   pub var favouriteActivity: String
  // }

  pub resource Stuff: ITest.IStuff { // implement the resource interface from the contract interface like this
    pub var favouriteActivity: String

    init() {
      self.favouriteActivity = "Playing League of Legends."
    }
  }

  init() {
    self.number = 0
  }
}
```
