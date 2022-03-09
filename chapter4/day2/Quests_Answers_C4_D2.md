## Quests Answers - Chapter 4, Day 2

1. What does `.link()` do?
> The `.link()` function is used to "link" resources to the `/public/` or `/private/` path. The resources are still stored in the `/storage/` path, `.link()` just creates a capability or "pointer" from the `/public/` or `/private/` path to the resource in the `/storage/` path.

2. In your own words (no code), explain how we can use resource interfaces to only expose certain things to the `/public/` path.
> The `.link()` function makes a resource publicly accessible by creating a capability that "links" to the resource from the `/public/` path. To obtain the capability, we must get a `PublicAccount` type of the address we're reading from using the `.getAccount()` function. We then `.borrow()` the capability to get a reference to the resource it's pointing to. By creating a resource interface and restricting the reference to it when we .link() the resource to the `/public/` path, we can limit access to the resource by exposing only certain things that we specify in the interface.


3. Deploy a contract that contains a resource that implements a resource interface. Then, do the following:

    1) In a transaction, save the resource to storage and link it to the public with the restrictive interface. 

    2) Run a script that tries to access a non-exposed field in the resource interface, and see the error pop up.

    3) Run the script and access something you CAN read from. Return it from the script.

```swift
/****************/
/*** Contract ***/
/****************/

pub contract MyContract {

  pub resource interface INFT {
    // we only expose the `project` variable
    pub var project: String
  }
  
  // NFT resource implements above resource interface INFT
  pub resource NFT: INFT {
    pub var id: Int
    pub var project: String

    init() {
      self.id = 1512
      self.project = "Hoodlums"
    }
  }

  pub fun createNFT(): @NFT {
    return <- create NFT()
  }

}
```

```swift
/*******************/
/*** Transaction ***/
/*******************/

import MyContract from 0x01

transaction() {
  prepare(signer: AuthAccount) {
    // save the resource to storage
    signer.save(<- MyContract.createNFT(), to: /storage/MyNFTResource)

    // link the resource with the restrictive interface to the public
    signer.link<&MyContract.NFT{MyContract.INFT}>(/public/MyNFTResource, target: /storage/MyNFTResource)
  }

  execute {

  }
}
```

```swift
/*************************/
/*** Script with error ***/
/*************************/

import MyContract from 0x01

pub fun main(address: Address): String {
  let publicCapability: Capability<&MyContract.NFT{MyContract.INFT}> =
    getAccount(address).getCapability<&MyContract.NFT{MyContract.INFT}>(/public/MyNFTResource)

  let NFT: &MyContract.NFT{MyContract.INFT} = publicCapability.borrow()
    ?? panic("The capability doesn't exist or you did not specify the right type when you got the capability.")

  // ERROR: "member of restricted type is not accessible: id"
  return NFT.id
}
```

```swift
/****************************/
/*** Script without error ***/
/****************************/

import MyContract from 0x01

pub fun main(address: Address): String {
  let publicCapability: Capability<&MyContract.NFT{MyContract.INFT}> =
    getAccount(address).getCapability<&MyContract.NFT{MyContract.INFT}>(/public/MyNFTResource)

  let NFT: &MyContract.NFT{MyContract.INFT} = publicCapability.borrow()
    ?? panic("The capability doesn't exist or you did not specify the right type when you got the capability.")

  // This works because `project` is exposed in the resource interface INFT`
  return NFT.project // "Hoodlums"
}
```
> Playground Link: https://play.onflow.org/d0969799-dce0-4544-8dd8-01b9ed1af6ff
