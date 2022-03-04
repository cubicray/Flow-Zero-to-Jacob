## Quests Answers - Chapter 3, Day 3

1. Define your own contract that stores a dictionary of resources. Add a function to get a reference to one of the resources in the dictionary.
```swift
pub contract Test {

    pub var myNFTs: @{Int: NFT}

    pub resource NFT {
        pub let id: Int
        pub let project: String
        pub let wallet: Address
        
        init(_id: Int, _project: String, _wallet: Address) {
            self.id = _id
            self.project = _project
            self.wallet = _wallet
        }
    }

    pub fun addNFT() {
        let newNFT <- create NFT(_id: 1512, _project: "Hoodlums", _wallet: 0x05)
        self.myNFTs[newNFT.id] <-! newNFT
    }

    pub fun getNFTreference(id: Int): &NFT {
        let NFTref = &self.myNFTs[id] as &NFT
        return NFTref
    }

    init() {
        self.myNFTs <- {}
    }

}
```

2. Create a script that reads information from that resource using the reference from the function you defined in part 1. 
```swift
import Test from 0x01

pub fun main() {
  let ref = Test.getNFTreference(id: 1512)
  log(ref.id)
  log(ref.project)
  log(ref.wallet)
}
```
> Playground Link: https://play.onflow.org/3a6efa24-fb25-4e40-b930-1a54b5b5f216

3. Explain, in your own words, why references can be useful in Cadence.
> With references it is easier to interact with resources. Without references we would need to move resources around a lot.
