## Quests Answers - Chapter 3, Day 2

Write your own smart contract that contains two state variables: an array of resources, and a dictionary of resources. Add functions to remove and add to each of them. They must be different from the examples above.

```swift
pub contract Test {

    pub var MyNFTsArray: @[NFT] // array of resources
    pub var MyNFTsDict: @{Int: NFT} // dictionary of resources

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

    // Functions for array of resources
    pub fun addNFTArray() {
        let newNFT <- create NFT(_id: 1512, _project: "Hoodlums", _wallet: 0x05)
        self.MyNFTsArray.append(<- newNFT)
    }

    pub fun transferNFTArray(index: Int): @NFT {
        let transferredNFT <- self.MyNFTsArray.remove(at: index)
        return <- transferredNFT
    }

    // Functions for dictionary of resources
    pub fun addNFTDict() {
        let newNFT <- create NFT(_id: 1512, _project: "Hoodlums", _wallet: 0x05)
        self.MyNFTsDict[newNFT.id] <-! newNFT
    }

    pub fun transferNFTDict(id: Int): @NFT {
        let transferredNFT <- self.MyNFTsDict.remove(key: id) ?? panic("Could not find the NFT to transfer!")
        return <- transferredNFT
    }

    init() {
        self.MyNFTsArray <- []
        self.MyNFTsDict <- {}
    }

}
```
