## Quests Answers - Chapter 4, Day 4

Because we had a LOT to talk about during this Chapter, I want you to do the following:

Take our NFT contract so far and add comments to every single resource or function explaining what it's doing in your own words.

```swift
pub contract CryptoPoops {
  pub var totalSupply: UInt64

  // This is an NFT resource that contains an id, name, favouriteFood, and luckyNumber.
  pub resource NFT {
    pub let id: UInt64

    pub let name: String
    pub let favouriteFood: String
    pub let luckyNumber: Int

    init(_name: String, _favouriteFood: String, _luckyNumber: Int) {
      // The id is set to self.uuid, which is a unique identifier on Flow
      // that is automatically set when a resource is created.
      // It ensures that each NFT is unique and has a different ID.
      self.id = self.uuid

      self.name = _name
      self.favouriteFood = _favouriteFood
      self.luckyNumber = _luckyNumber
    }
  }

  // This resource interface allows us to restrict to the public certain parts of our NFT Collection resource.
  // It only exposes the deposit(), getIDs(), and borrowNFT() functions to the public.
  // It allows us to link our resource securely without giving the public access to the withdraw() function.
  pub resource interface CollectionPublic {
    pub fun deposit(token: @NFT)
    pub fun getIDs(): [UInt64]
    pub fun borrowNFT(id: UInt64): &NFT
  }
  
  // We define a Collection resource which allows us to efficiently bundle all NFTs together
  // and store them at one storage path.
  // The Collection resource implements the above resource interface called CollectionPublic.
  pub resource Collection: CollectionPublic {
    // This dictionary maps an NFT resource type and its unique ID to its unique ID.
    pub var ownedNFTs: @{UInt64: NFT}
    
    // This function allows us to add an NFT to the Collection.
    pub fun deposit(token: @NFT) {
      self.ownedNFTs[token.id] <-! token
    }
    
    // This function allows us to remove an NFT from the Collection.
    pub fun withdraw(withdrawID: UInt64): @NFT {
      let nft <- self.ownedNFTs.remove(key: withdrawID) 
              ?? panic("This NFT does not exist in this Collection.")
      return <- nft
    }

    // This function returns an array of all the NFT ids in the Collection.
    pub fun getIDs(): [UInt64] {
      return self.ownedNFTs.keys
    }

    // This function enables us to read NFTs without having to withdraw them from the Collection.
    pub fun borrowNFT(id: UInt64): &NFT {
      return &self.ownedNFTs[id] as &NFT
    }

    init() {
      self.ownedNFTs <- {}
    }

    // This function makes sure that nested NFT resources are destroyed
    // when the main Collection resource is destroyed.
    destroy() {
      destroy self.ownedNFTs
    }
  }

  // This function creates a new Collection resource and returns it.
  pub fun createEmptyCollection(): @Collection {
    return <- create Collection()
  }

  // The Minter resource enables us to control who can mint an NFT
  // to prevent everyone from miniting their own NFTs.
  // Anyone who holds this Minter resource in their account storage has the ability to mint an NFT.
  // This "admin" functionality is usually given to the account that deploys the contract.
  pub resource Minter {
    // This function mints a new NFT resource.
    pub fun createNFT(name: String, favouriteFood: String, luckyNumber: Int): @NFT {
      return <- create NFT(_name: name, _favouriteFood: favouriteFood, _luckyNumber: luckyNumber)
    }
    
    // This function allows an account who already has the Minter resource
    // to give the Minter resouce to another account.
    // This could enable multiple people to mint NFTs.
    pub fun createMinter(): @Minter {
      return <- create Minter()
    }

  }

  init() {
    // Set the totalSupply to 0 initially.
    self.totalSupply = 0

    // Automatically save the Minter resource to account storage
    // to give the account that is deploying the contract the ability to mint NFTs.
    // This is possible because inside a contract's init function we have access to
    // the AuthAccount of the account that is deploying the contract.
    self.account.save(<- create Minter(), to: /storage/Minter)
  }
}
```
