#The WebWallet API
###A digital wallet specification for the Web.

## Abstract
This specification defines an API that aims to enable web applications to carry out electronic commerce transactions using WebWallets.

## What is a WebWallet?
A WebWallet is a digital wallet that can be recharged using cash to make payments on the web.

+ **No deposits, but recharges**  
A WebWallet is not a deposit account, but a rechargeable digital wallet. Funds are not added as deposits but as prepaid recharges, which represent a temporary intermediation between online buyers and sellers.

+ **No withdrawals, only payments**  
A WebWallet recharge is interpreted as a purchase intention, therefore it cannot be reversed. The balance loaded on a WebWallet cannot be withdrawn or redeemed for cash, so it must be fully spent at some point.

+ **No currency exchange**  
A WebWallet can be recharged in any currency supported by the involved parties and the applicable regulations. However, a WebWallet recharge is not a currency exchange operation by itself.


## WebWallet Transactions
WebWallet transactions are bilateral, token-based and expire if not completed within a certain time interval.

+ **Bilateral**  
  - WebWallet transactions are carried out in two major stages: creation and completion.  
  - Each of the two parties involved must exclusively carry out one of the two stages.  
  - A transaction initiated by one party can only be completed by the other party.  

+ **Token-based**  
  - WebWallet transactions are initiated by requesting a transaction token to a WebWallet provider.  
  - Transaction tokens are pointers to information such as the transaction type, amount and currency.  
  - Tokens must be handed over by the initiator party for the other party to complete the transaction.  

+ **Expirable**  
  - Transaction tokens must have an expiration date and receive a timestamp when they are created.  
  - Transactions must be completed by the party that did not create the token before the expiration date.  
  - If a transaction token expires, the initiator party will need to request a new token and start over.  

## API Overview
The WebWallet API defines how to expose and interact with WebWallets in order to request and carry out electronic commerce transactions.

* **Exposure through URIs**  
Each WebWallet is represented as a URI:  

    ```
    wallet.example.com/:walletID
    ```

* **Secure API calls using tokens**  
A transaction token must be requested in order to obtain authorization to make API calls:

    ```
    POST /:walletID/transactions HTTP/1.1
    Host: wallet.example.com
    
    options=transaction-options
    ```

* **Transactions through HTTP methods**  
Transactions are carried out using HTTP methods signed with a valid transaction token.

    **Checking the balance:**
    ```
    GET /:walletID/balance HTTP/1.1
        ?token=transaction-token
    Host: wallet.example.com
    ```

    **Adding funds:**
    ```
    POST /:walletID/funds HTTP/1.1
    Host: wallet.example.com
    
    token=transaction-token
    ```
    
    **Sending/Requesting a payment:**
    ```
    POST /:walletID/payments HTTP/1.1
    Host: wallet.example.com
    
    token=transaction-token
    ```
