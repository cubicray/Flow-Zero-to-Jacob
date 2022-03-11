## Quests Answers - Chapter 4, Day 3

1. What do you have to do if you have resources "nested" inside of another resource? ("Nested resources")
> We must make sure that nested resources are destroyed when we destroy the main resource. We do that by specifying a `destroy()` function this will destroy the nested resources inside of the parent resource.

2. Brainstorm some extra things we may want to add to this contract. Think about what might be problematic with this contract and how we could fix it.

Idea 1: Do we really want everyone to be able to mint an NFT?

> It is common to have a private whitelist mint before a public mint. Accounts/addresses that are allowed to mint can be collected and a resource interface could be used to only give mint access to these whitelisted accounts. Other restrictions and requirements for minting can also be implemented, such as the maximum amount of NFTs allowed to mint per account, and having a minimum amount of a certain token in an account before being able to mint.

Idea 2: If we want to read information about our NFTs inside our Collection, right now we have to take it out of the Collection to do so. Is this good?

> A more practical and secure way is to use the `borrow()` function. `borrow()` enables us to look at our NFTs without moving them because they return references of the NFTs inside our Collection. THis way the actual NFTs don't leave the account storage.
