## Quests Answers - Chapter 2, Day 2

1. Explain why we wouldn't call `changeGreeting` in a script.

> Scripts are used to view data and don't require payment to be executed. Transactions are used to modify data and require payment to be executed. We call the function `changeGreeting` from inside a transaction and not a script because it modifies data on the blockchain (it is changing the variable called `greeting`).

2. What does the `AuthAccount` mean in the `prepare` phase of the transaction?

> `AuthAccount` refers to the user's account from which data will be accessed when a user sends/approves (pays and signs) a transaction.

3. What is the difference between the `prepare` phase and the `execute` phase in the transaction?

> The `prepare` phase is used to access data in the user's account. The `execute` phase does not access data in the user's account. The `execute` phase is used to call functions that change data on the blockchain.

4. This is the hardest quest so far, so if it takes you some time, do not worry! I can help you in the Discord if you have questions.

- Add two new things inside your contract:
    - A variable named `myNumber` that has type `Int` (set it to 0 when the contract is deployed)
    - A function named `updateMyNumber` that takes in a new number named `newNumber` as a parameter that has type `Int` and updates `myNumber` to be `newNumber`
> <img src="/chapter2/day2/screenshot_01_C2_D2.png" alt="screenshot" size="400">

- Add a script that reads `myNumber` from the contract
> <img src="/chapter2/day2/screenshot_02_C2_D2.png" alt="screenshot" size="400">

- Add a transaction that takes in a parameter named `myNewNumber` and passes it into the `updateMyNumber` function. Verify that your number changed by running the script again.
> <img src="/chapter2/day2/screenshot_03_C2_D2.png" alt="screenshot" size="400">
