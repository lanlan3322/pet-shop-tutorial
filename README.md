Source: https://www.trufflesuite.com/tutorial



This tutorial will take you through the process of building your first dapp---an adoption tracking system for a pet shop!

This tutorial is meant for those with a basic knowledge of Ethereum and smart contracts, who have some knowledge of HTML and JavaScript, but who are new to dapps.

**Note**: For Ethereum basics, please read the Truffle  [Ethereum Overview](https://www.trufflesuite.com/guides/ethereum-overview)  tutorial before proceeding.

In this tutorial we will be covering:

1.  Setting up the development environment
2.  Creating a Truffle project using a Truffle Box
3.  Writing the smart contract
4.  Compiling and migrating the smart contract
5.  Testing the smart contract
6.  Creating a user interface to interact with the smart contract
7.  Interacting with the dapp in a browser

## Background

Pete Scandlon of Pete's Pet Shop is interested in using Ethereum as an efficient way to handle their pet adoptions. The store has space for 16 pets at a given time, and they already have a database of pets. As an initial proof of concept,  **Pete wants to see a dapp which associates an Ethereum address with a pet to be adopted.**

The website structure and styling will be supplied.  **Our job is to write the smart contract and front-end logic for its usage.**

## [](https://www.trufflesuite.com/tutorials/pet-shop#setting-up-the-development-environment)Setting up the development environment

There are a few technical requirements before we start. Please install the following:

-   [Node.js v8+ LTS and npm](https://nodejs.org/en/)  (comes with Node)


Once we have those installed, we only need one command to install Truffle:

 ![Open cmd window](https://github.com/lanlan3322/pet-shop-tutorial/blob/master/cmd.png "Open cmd window")
    

```shell
npm install -g truffle
```

To verify that Truffle is installed properly, type  `truffle version`  on a terminal. If you see an error, make sure that your npm modules are added to your path.

We also will be using  [Ganache](https://www.trufflesuite.com/ganache), a personal blockchain for Ethereum development you can use to deploy contracts, develop applications, and run tests. You can download Ganache by navigating to  [http://truffleframework.com/ganache](http://truffleframework.com/ganache)  and clicking the "Download" button.

**Note**: If you are developing in an environment without a graphical interface, you can also use Truffle Develop, Truffle's built-in personal blockchain, instead of Ganache. You will need to change some settings---such as the port the blockchain runs on---to adapt the tutorial for Truffle Develop.


1.  In a terminal, make sure you are in the root of the directory that contains the dapp and type:
    
    ```shell
    truffle compile
    ```
    
    **Note**: If you're on Windows and encountering problems running this command, please see the documentation on  [resolving naming conflicts on Windows](https://www.trufflesuite.com/docs/truffle/reference/configuration#resolving-naming-conflicts-on-windows).
    
    You should see output similar to the following:
    
    ```shell
    Compiling your contracts...
    ===========================
    > Compiling ./contracts/Adoption.sol
    > Compiling ./contracts/Migrations.sol
    > Artifacts written to /Users/cruzmolina/Code/truffle-projects/metacoin/build/contracts
    > Compiled successfully using:
       - solc: 0.5.0+commit.1d4f565a.Emscripten.clang
    ```
    
    
2.  Back in our terminal, migrate the contract to the blockchain.
    
    ```shell
    truffle migrate
    ```
    
    You should see output similar to the following:
    

```shell
  1_initial_migration.js
  ======================

     Deploying 'Migrations'
     ----------------------
     > transaction hash:    0x3b558e9cdf1231d8ffb3445cb2f9fb01de9d0363e0b97a17f9517da318c2e5af
     > Blocks: 0            Seconds: 0
     > contract address:    0x5ccb4dc04600cffA8a67197d5b644ae71856aEE4
     > account:             0x8d9606F90B6CA5D856A9f0867a82a645e2DfFf37
     > balance:             99.99430184
     > gas used:            284908
     > gas price:           20 gwei
     > value sent:          0 ETH
     > total cost:          0.00569816 ETH


     > Saving migration to chain.
     > Saving artifacts
     -------------------------------------
     > Total cost:          0.00569816 ETH


  2_deploy_contracts.js
  =====================

     Deploying 'Adoption'
     .............................
     .............................
```

You can see the migrations being executed in order, followed by some information related to each migration. (Your information will differ.)

In Ganache, note that the state of the blockchain has changed. The blockchain now shows that the current block, previously  `0`, is now  `4`. In addition, while the first account originally had 100 ether, it is now lower, due to the transaction costs of migration. We'll talk more about transaction costs later.
    
    ![Ganache after migration](https://www.trufflesuite.com/img/tutorials/pet-shop/ganache-migrated.png "Ganache after migration")
    
    Ganache after migration
    

3.  Back in the terminal, run the tests:
    
    ```shell
    truffle test
    ```
    
 If all the tests pass, you'll see console output similar to this:
    

```shell
   Using network 'development'.

   Compiling your contracts...
   ===========================
   > Compiling ./test/TestAdoption.sol
   > Artifacts written to /var/folders/z3/v0sd04ys11q2sh8tq38mz30c0000gn/T/test-11934-19747-g49sra.0ncrr
   > Compiled successfully using:
      - solc: 0.5.0+commit.1d4f565a.Emscripten.clang

     TestAdoption
       ✓ testUserCanAdoptPet (91ms)
       ✓ testGetAdopterAddressByPetId (70ms)
       ✓ testGetAdopterAddressByPetIdInArray (89ms)


     3 passing (670ms)
```

    
4.  Now we need to connect MetaMask to the blockchain created by Ganache. Click the menu that shows "Main Network" and select  **Custom RPC**.
    
    ![MetaMask network menu](https://www.trufflesuite.com/img/tutorials/pet-shop/metamask-networkmenu.png "MetaMask network menu")
    
    MetaMask network menu
    
5.  In the box titled "New Network" enter  `http://127.0.0.1:7545`  and click  **Save**.
    
    ![MetaMask Custom RPC](https://www.trufflesuite.com/img/tutorials/pet-shop/metamask-customrpc.png "MetaMask Custom RPC")
    
    MetaMask Custom RPC
    
    The network name at the top will switch to say  `http://127.0.0.1:7545`.
    
6.  Click the top-right X to close out of Settings and return to the Accounts page.
    
    Each account created by Ganache is given 100 ether. You'll notice it's slightly less on the first account because some gas was used when the contract itself was deployed and when the tests were run.
    
    ![MetaMask account configured](https://www.trufflesuite.com/img/tutorials/pet-shop/metamask-account1.png "MetaMask account configured")
    
    MetaMask account configured
    
    Configuration is now complete.

7.  Start the local web server:
    
    ```shell
    npm run dev
    ```
    
    The dev server will launch and automatically open a new browser tab containing your dapp.
    
    ![Pete's Pet Shop](https://www.trufflesuite.com/img/tutorials/pet-shop/dapp.png "Pete's Pet Shop")
    
    Pete's Pet Shop
    
8.  A MetaMask pop-up should appear requesting your approval to allow Pete's Pet Shop to connect to your MetaMask wallet. Without explicit approval, you will be unable to interact with the dapp. Click  **Connect**.
    
    ![MetaMask approval request](https://www.trufflesuite.com/img/tutorials/pet-shop/metamask-transactionconfirm.png "MetaMask approval request")
    
    MetaMask approval request
    
9.  To use the dapp, click the  **Adopt**  button on the pet of your choice.
    
10.  You'll be automatically prompted to approve the transaction by MetaMask. Click  **Submit**  to approve the transaction.
    
    ![Adoption transaction review](https://www.trufflesuite.com/img/tutorials/pet-shop/metamask-transactionconfirm.png "Adoption transaction review")
    
    Adoption transaction review
    
11.  You'll see the button next to the adopted pet change to say "Success" and become disabled, just as we specified, because the pet has now been adopted.
    
    ![Adoption success](https://www.trufflesuite.com/img/tutorials/pet-shop/dapp-success.png "Adoption success")
    
    Adoption success
    
    **Note**: If the button doesn't automatically change to say "Success", refreshing the app in the browser should trigger it.
    
    And in MetaMask, you'll see the transaction listed:
    
    ![MetaMask transaction](https://www.trufflesuite.com/img/tutorials/pet-shop/metamask-transactionsuccess.png "MetaMask transaction")
    
    MetaMask transaction
    
    You'll also see the same transaction listed in Ganache under the "Transactions" section.
    

Congratulations! You have taken a huge step to becoming a full-fledged dapp developer. For developing locally, you have all the tools you need to start making more advanced dapps. If you'd like to make your dapp live for others to use, stay tuned for our future tutorial on deploying to the Ropsten testnet.
