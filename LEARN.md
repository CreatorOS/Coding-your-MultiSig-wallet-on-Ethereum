# Coding your MultiSig wallet on Ethereum
In this quest, we will build a 2-of-2 multi-signature wallet using Ethereum’s Solidity. But first, what is a multi-signature wallet? A multi-signature wallet, AKA MultiSig wallet, is a wallet that is owned by more than one owner and demands more than one signature to send a transaction. An n-of-m MultiSig wallet requires that n out of m owners confirm (sign) the transaction so it gets sent. In our case, our wallet has two owners and both of them have to sign a transaction before funds get released. You can use such a wallet if you trust someone and you would like to create a shared address with him/her. Or maybe, you can use it to secure your money by creating two different private keys and storing them in different places. This way, if an attacker steals on key, he/she will not be able to use your money. Without further ado, let’s get our hands on the keyboard!
## Starting with the smart contract:
Ok, now open your IDE and write these on top of your code:

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/ef41794d-5f8f-4786-8d7e-83b8a7c2a30e.jpg)

This is just regular practice, adding a license identifier and specifying solidity version.  And you can see ABIEncoderV2 which you may need when writing a contract. It makes it possible to return arrays from functions and passing structs to functions without workarounds.

Now, let’s cut to the chase and create state variables and a constructor.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/b0a45006-8c41-4f68-b7ef-dc5b9d97d3c7.jpg)

In the code snippet above, you can see two addresses representing two owners. And a Transaction, which is a struct that contains the necessary details to confirm a 2-of-2 wallet’s transaction. There is a field of type *address payable *to allow the receiver to receive funds, a *uint *to store the value to Ethers to be transferred, and two *bool *variables to keep track of who signed what. Transactions are stored in a dynamic array called *transactions. *Then there is a constructor that simply assigns the owners to whatever the deployer requested. So if you want to share a 2-of-2 wallet with your best friend, you have to provide both of your addresses. Finally, there is the famous *onlyOwner *modifier to help restrict function calls to owners.
## Initiating a transaction:
We want our wallet to let us send transactions. How can we do that programmatically? Well, first we should create a function that allows an owner to request a transaction. Then we need a function that allows the other owner to approve, given he/she wants to approve. Take a look at this function:

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/e80ea1d6-8d8f-409a-b9f7-a886584bb080.jpg)

This is a function restricted to owners, no one other than them can call it. It takes two parameters, the intended destination of the transactions and the number of Ethers to be sent. A new object of type transaction is created every time this function is called. Its fields get populated respectively: the *to *and *amount *fields are received directly from the function’s parameters. Then the transaction gets signed by whoever called the function. And then the transaction gets added to the array of transactions, waiting to get approved. That is why we used the keyword *memory *and not *storage *for the *transaction, *we just need it to store information in an intermediary step, then it gets stored in the array so no need to keep it in storage.
## approving a transaction:
Now you initiated a transaction, but it is still just a request for now. It needs to get approved by the second owner. So we need a function that allows an owner to approve transactions. This can be done easily by signing the transaction using one of the boolean fields in each transaction:

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/e3e57f38-fc79-48e4-a81f-5744b819af32.jpg)

So if you are an owner and you would like to approve a transaction, you provide its id as a parameter to this function. The function then checks what bool field to flip to true indicating that you have signed. Now that both owners are on board, we can safely transfer the funds. The latter step is achieved using a function called *withdraw. *Now take a breath and get ready, we are moving to the most important function in this contract.
## executing a transaction:
What now? We have the transaction initiated and signed, what else? Now we just need to run a couple of checks for security and release the funds.

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/a59d1920-8305-4a88-9e8b-09cb2610f31d.jpg)

Now I know what you are thinking, why did I add a check that the id of the transaction is less than the array length if I am calling *withdraw *from *approveTransaction* and this condition is checked there. It is a good practice to add checks as you go. Suppose someone decides to call *withdraw *from a frontend for example with a wrong id, it will be a problem since there are no checks for it. Then you check if there is enough money in the wallet’s balance. Then a *require()* to ensure that the transaction is signed by the owners. Finally, if the amount of Ethers is not zero, release the funds to the designated address. Then set the amount to zero to mark that the money is spent (and it is useful in your frontend in case you want to show pending transactions - you can search for transactions with amount > 0).
## Last touches & conclusion:
It is always beneficial to add utility functions that you think can be useful. 

![](https://qb-content-staging.s3.ap-south-1.amazonaws.com/public/fb231f7d-06af-4aff-bca3-fd51cb633f77/a08f991b-b816-47d2-8c5b-b32d8bfbeea9.jpg)

Note that solidity gives you a getter function for all public state fields for free. So we have a getter function for *transactions. *This implies that the *getTransactions *function is useless, right? Well not exactly, getters for arrays expect a specific index and return only the element in that index. Meanwhile, *getTransactions *returns the whole array at once thanks to ABIEncoderV2.

So, now you have coded a MultiSig wallet on Ethereum! Feel free to deploy it, try it, and build on it. Happy coding!