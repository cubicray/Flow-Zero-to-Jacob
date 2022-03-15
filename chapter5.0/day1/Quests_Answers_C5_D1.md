## Quests Answers - Chapter 5, Day 1

1. Describe what an event is, and why it might be useful to a client.

>Events declared in a smart contract's body allow us to notify and broadcast data to the blockchain when something happens. We trigger the event with an `emit` statement when the contract is executed. Clients listening to our contract can then react to the event by updating their code. For example, a client could update a counter on their website each time an NFT is minted.

2. Deploy a contract with an event in it, and emit the event somewhere else in the contract indicating that it happened.

```swift
answer
```

3. Using the contract in step 2), add some pre conditions and post conditions to your contract to get used to writing them out.

```swift
answer
```

4. For each of the functions below (numberOne, numberTwo, numberThree), follow the instructions.

```swift
pub contract Test {

  // TODO
  // Tell me whether or not this function will log the name.
  // name: 'Jacob'

  // Answer:
  // This function will log the name "Jacob" because it satisfies the pre-condition,
  // which requires the name to be exactly five characters long.
  
  pub fun numberOne(name: String) {
    pre {
      name.length == 5: "This name is not cool enough."
    }
    log(name)
  }

  // TODO
  // Tell me whether or not this function will return a value.
  // name: 'Jacob'

  // Answer:
  // This function will return the value "Jacob Tucker"
  // as long as we provide "Jacob" as the parameter to the function,
  // so that the post-condition can be satisfied.
  
  pub fun numberTwo(name: String): String {
    pre {
      name.length >= 0: "You must input a valid name."
    }
    post {
      result == "Jacob Tucker"
    }
    return name.concat(" Tucker")
  }

  pub resource TestResource {
    pub var number: Int

    // TODO
    // Tell me whether or not this function will log the updated number.
    // Also, tell me the value of `self.number` after it's run.

    // Answer:
    // This function will not log the updated number because the post-condition fails.
    // `before()` returns the value of `self.number` before it was changed by `numberThree()`, so: before(self.number) = 0
    // `result` variable equals the value of what's being returned, which is 0 + 1, so: return = 1
    // So the post-condition `before(self.number) == result + 1` is the same as `0 == 1 + 1`, which is false.

    // The post-condition fails, which means the transaction panics and aborts.
    // Hence the value of `self.number` remains 0.
    // If the post-condition would be true, the value of `self.number` after it's run would be 1.

    pub fun numberThree(): Int {
      post {
        before(self.number) == result + 1
      }
      self.number = self.number + 1
      return self.number
    }

    init() {
      self.number = 0
    }

  }

}
```
