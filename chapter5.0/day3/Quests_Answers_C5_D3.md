## Quests Answers - Chapter 5, Day 3

1. What does "force casting" with `as!` do? Why is it useful in our Collection?
>"Force casting" acts like a filter. It allows us to extract a specific type from a generic type, i.e. it "downcasts" a generic type to a more specific subtype. We use it in our Collection resource to ensure that only our specific type of NFT can be deposited into our Collection.

2. What does `auth` do? When do we use it?
>`auth` allows us to get an "authorized reference". We need an `auth` reference when we want to "downcast" references from a generic type to a more specific subtype. We can use it when we want to read all the metadata of our whole NFT and not just the generic NFT from the standard.

3. This last quest will be your most difficult yet. Take this contract:

```swift
import NonFungibleToken from 0x02
pub contract CryptoPoops: NonFungibleToken {
  pub var totalSupply: UInt64

  pub event ContractInitialized()
  pub event Withdraw(id: UInt64, from: Address?)
  pub event Deposit(id: UInt64, to: Address?)

  pub resource NFT: NonFungibleToken.INFT {
    pub let id: UInt64

    pub let name: String
    pub let favouriteFood: String
    pub let luckyNumber: Int

    init(_name: String, _favouriteFood: String, _luckyNumber: Int) {
      self.id = self.uuid

      self.name = _name
      self.favouriteFood = _favouriteFood
      self.luckyNumber = _luckyNumber
    }
  }

  pub resource Collection: NonFungibleToken.Provider, NonFungibleToken.Receiver, NonFungibleToken.CollectionPublic {
    pub var ownedNFTs: @{UInt64: NonFungibleToken.NFT}

    pub fun withdraw(withdrawID: UInt64): @NonFungibleToken.NFT {
      let nft <- self.ownedNFTs.remove(key: withdrawID) 
            ?? panic("This NFT does not exist in this Collection.")
      emit Withdraw(id: nft.id, from: self.owner?.address)
      return <- nft
    }

    pub fun deposit(token: @NonFungibleToken.NFT) {
      let nft <- token as! @NFT
      emit Deposit(id: nft.id, to: self.owner?.address)
      self.ownedNFTs[nft.id] <-! nft
    }

    pub fun getIDs(): [UInt64] {
      return self.ownedNFTs.keys
    }

    pub fun borrowNFT(id: UInt64): &NonFungibleToken.NFT {
      return &self.ownedNFTs[id] as &NonFungibleToken.NFT
    }

    init() {
      self.ownedNFTs <- {}
    }

    destroy() {
      destroy self.ownedNFTs
    }
  }

  pub fun createEmptyCollection(): @NonFungibleToken.Collection {
    return <- create Collection()
  }

  pub resource Minter {

    pub fun createNFT(name: String, favouriteFood: String, luckyNumber: Int): @NFT {
      return <- create NFT(_name: name, _favouriteFood: favouriteFood, _luckyNumber: luckyNumber)
    }

    pub fun createMinter(): @Minter {
      return <- create Minter()
    }

  }

  init() {
    self.totalSupply = 0
    emit ContractInitialized()
    self.account.save(<- create Minter(), to: /storage/Minter)
  }
}
```

and add a function called `borrowAuthNFT` just like we did in the section called "The Problem" above. Then, find a way to make it publically accessible to other people so they can read our NFT's metadata. Then, run a script to display the NFTs metadata for a certain `id`.

You will have to write all the transactions to set up the accounts, mint the NFTs, and then the scripts to read the NFT's metadata. We have done most of this in the chapters up to this point, so you can look for help there :)

> Playground Link: https://play.onflow.org/8d6e8255-b7f0-4437-b974-93490edae404

>Contract
```swift
import NonFungibleToken from 0x02

// Use the : {contract interface} syntax to implement the `NonFungibleToken` contract interface
pub contract CryptoPoops: NonFungibleToken {
  // Total number of NFTs in existence.
  pub var totalSupply: UInt64
  
  // Events
  pub event ContractInitialized() // emit this event when the contract gets initialized
  pub event Withdraw(id: UInt64, from: Address?) // emit this event when we withdraw an NFT
  pub event Deposit(id: UInt64, to: Address?)

  // This is an NFT resource that contains an id, name, favouriteFood, and luckyNumber.
  // It implements the INFT resource interface from the `NonFungibleToken` contract interface (NFT standard).
  pub resource NFT: NonFungibleToken.INFT {
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
  
  // Make borrowAuthNFT() accessible to the public when we link the Collection to the public
  // by adding it to this resource interface.
  pub resource interface MyCollectionPublic {
    pub fun deposit(token: @NonFungibleToken.NFT)
    pub fun getIDs(): [UInt64]
    pub fun borrowNFT(id: UInt64): &NonFungibleToken.NFT
    pub fun borrowAuthNFT(id: UInt64): &NFT
  }

  // We define a Collection resource which allows us to efficiently bundle all NFTs together
  // and store them at one storage path.
  // The Collection resource implements our above resource interface called MyCollectionPublic.
  // It also implements the Provider, Receiver, and CollectionPublic resource interfaces from the NFT standard.
  pub resource Collection: NonFungibleToken.Provider, NonFungibleToken.Receiver, NonFungibleToken.CollectionPublic, MyCollectionPublic {
    // This dictionary maps an NFT resource type and its unique ID to its unique ID.
    pub var ownedNFTs: @{UInt64: NonFungibleToken.NFT}
    
    // This function allows us to remove an NFT from the Collection.
    pub fun withdraw(withdrawID: UInt64): @NonFungibleToken.NFT {
      let nft <- self.ownedNFTs.remove(key: withdrawID) 
              ?? panic("This NFT does not exist in this Collection.")
      // Emit the Withdraw event.
      emit Withdraw(id: nft.id, from: self.owner?.address)
      return <- nft
    }

    // This function allows us to add an NFT to the Collection.
    pub fun deposit(token: @NonFungibleToken.NFT) {
      // "force cast" the generic standard `token` NFT type to our specific `@NFT` CryptoPoop NFT type.
      // "downcasting" ensures that only our specific CryptoPoop NFTs can be deposited into our Collection.
      let nft <- token as! @NFT
      // Emit the Deposit event.
      emit Deposit(id: nft.id, to: self.owner?.address)
      self.ownedNFTs[nft.id] <-! nft
    }
    
    // This function returns an array of all the NFT ids in the Collection.
    pub fun getIDs(): [UInt64] {
      return self.ownedNFTs.keys
    }

    // This function enables us to read NFTs (IDs only) without having to withdraw them from the Collection.
    pub fun borrowNFT(id: UInt64): &NonFungibleToken.NFT {
      return &self.ownedNFTs[id] as &NonFungibleToken.NFT
    }
    
    // This function enables us to read our whole CryptoPoop NFT with all its custom attributes.
    // First we use `auth` to get an "authorized reference" to &NonFungibleToken.NFT,
    // then we use 'as!` to "downcast" the "authorized reference" to our CryptoPoop NFT type to access all the metadata.
    pub fun borrowAuthNFT(id: UInt64): &NFT {
      let ref = &self.ownedNFTs[id] as auth &NonFungibleToken.NFT
      return ref as! &NFT
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
  pub fun createEmptyCollection(): @NonFungibleToken.Collection {
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

    // Emit the ContractInitialized event.
    emit ContractInitialized()

    // Automatically save the Minter resource to account storage
    // to give the account that is deploying the contract the ability to mint NFTs.
    // This is possible because inside a contract's init function we have access to
    // the AuthAccount of the account that is deploying the contract.
    self.account.save(<- create Minter(), to: /storage/Minter)
  }
}
```

>Transaction: Create Collection
```swift
import CryptoPoops from 0x01
import NonFungibleToken from 0x02

// Script to create and store an NFT collection,
// and link it to the public through resource interfaces.
transaction {
  prepare(acct: AuthAccount) {
    acct.save(<- CryptoPoops.createEmptyCollection(), to: /storage/Collection)
    acct.link<&CryptoPoops.Collection{NonFungibleToken.CollectionPublic, CryptoPoops.MyCollectionPublic}>(/public/Collection, target: /storage/Collection)   
  }

  execute {
    log("Stored a Collection for our CryptoPoops")
  }
}
```

>Transaction: Mint NFT
```swift
import CryptoPoops from 0x01

// Script to mint an NFT with custom metadata via the Minter resource
transaction(recipient: Address, name: String, favouriteFood: String, luckyNumber: Int) {
  
  prepare(acct: AuthAccount) {
    let nftMinter = acct.borrow<&CryptoPoops.Minter>(from: /storage/Minter)!

    let publicReference = getAccount(recipient).getCapability(/public/Collection)
                            .borrow<&CryptoPoops.Collection{CryptoPoops.MyCollectionPublic}>()
                            ?? panic("This account does not have a Collection")

    publicReference.deposit(token: <- nftMinter.createNFT(name: name, favouriteFood: favouriteFood, luckyNumber: luckyNumber))
  }

  execute {
    log("Stored newly minted NFT into Collection")
  }
}
```

>Script: Read IDs
```swift
import CryptoPoops from 0x01
import NonFungibleToken from 0x02

// Script to return a list of NFT IDs inside our Collection,
// using the publicly accessible getIDs() function on a public reference to our Collection.
pub fun main(account: Address): [UInt64] {
  let publicReference = getAccount(account).getCapability(/public/Collection)
                            .borrow<&CryptoPoops.Collection{NonFungibleToken.CollectionPublic}>()
                            ?? panic("This account does not have a Collection")

  return publicReference.getIDs()
}
```

>Script: Read Metadata
```swift
import CryptoPoops from 0x01

// Script to display an NFT's metadata for a certain id
pub fun main(account: Address, id: UInt64) {
  let publicReference = getAccount(account).getCapability(/public/Collection)
                            .borrow<&CryptoPoops.Collection{CryptoPoops.MyCollectionPublic}>()
                            ?? panic("This account does not have a Collection")

  let nftRef = publicReference.borrowAuthNFT(id: id)
  
  log(nftRef.name)
  log(nftRef.favouriteFood)
  log(nftRef.luckyNumber)
}
```
