## Quests Answers - Chapter 4, Day 1

1. Explain what lives inside of an account. 
> 1. Contract Code: Multiple contracts can be stored in an account.
> 2. Account Storage: This is where all the data of the account gets stored.

2. What is the difference between the `/storage/`, `/public/`, and `/private/` paths?
> - `/storage/`: This is the path to where everything is stored. It's only accessible by the owner.
> - `/public/`: Data in this path is accessible to everyone.
> - `/private/`: Data in this path can only be accessed by the owner and people the owner gives access to.

3. What does `.save()` do? What does `.load()` do? What does `.borrow()` do?
> - `.save()`: Allows us to store data under an account's `/storage/` path.
> - `.load()`: Used to move data out of account storage (returns an optional).
> - `.borrow()`: Enables us to look at data in our account without moving it. It gets a reference to the data instead of the actual data, which means the data doesn't leave the account storage.

4. Explain why we couldn't save something to our account storage inside of a script.
> `AuthAccount` which refers to the user's account can only be retrieved in the `prepare` phase of a signed transaction. Without `AuthAccount` we cannot access the account's storage.

5. Explain why I couldn't save something to your account.
> Only the account owner can store data under account `/storage/`, after signing a transaction that gives them access to their `AuthAccount`.

6. Define a contract that returns a resource that has at least 1 field in it. Then, write 2 transactions:

    1) A transaction that first saves the resource to account storage, then loads it out of account storage, logs a field inside the resource, and destroys it.

    2) A transaction that first saves the resource to account storage, then borrows a reference to it, and logs a field inside the resource.

```swift
pub contract MyContract {

  pub resource NFT {
    pub var project: String

    init() {
      self.project = "Hoodlums"
    }
  }

  pub fun createNFT(): @NFT {
    return <- create NFT()
  }

}
```

```swift
import MyContract from 0x01

transaction() {
  prepare(signer: AuthAccount) {
    let testNFT <- MyContract.createNFT()

    // save the resource to account storage
    signer.save(<- testNFT, to: /storage/MyTestNFT)

    // load resource out of account storage
    let loadNFT <- signer.load<@MyContract.NFT>(from: /storage/MyTestNFT)
                          ?? panic("NFT resource not found.")
    
    // log a field inside the resource
    log(loadNFT.project) // "Hoodlums"

    // destroy resource
    destroy loadNFT
  }

  execute {

  }
}
```

```swift
import MyContract from 0x01

transaction() {
  prepare(signer: AuthAccount) {
    let testNFT <- MyContract.createNFT()
    
    // save the resource to account storage
    signer.save(<- testNFT, to: /storage/MyTestNFT)

    // borrow a reference to the resource
    let borrowNFT = signer.borrow<&MyContract.NFT>(from: /storage/MyTestNFT)
                          ?? panic("NFT resource not found.")
    
    // log a field inside the resource
    log(borrowNFT.project) // "Hoodlums"
  }

  execute {

  }
}
```
> Playground Link: https://play.onflow.org/c912ffc0-c7fd-4be0-9938-53df1d01dd1e
