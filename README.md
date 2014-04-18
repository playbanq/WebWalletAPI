#The WebWallet API
###A digital wallet specification for the Web.

## Abstract
This specification defines an API that aims to enable web applications to carry out electronic commerce transactions using WebWallets.

## What is a WebWallet?
A WebWallet is a digital wallet that can temporarily store money on the Web for intended online purchases.

+ **No deposits, but recharges**  
A WebWallet is funded through prepaid recharges, and funds can be added in any currency supported by the target merchant and the applicable regulations. 

+ **No retention, but intermediation**  
A WebWallet is not a savings deposit, but a temporary intermediary between online buyers and sellers, so it is not intended for long term storage of large amounts of money.

+ **No withdrawals, only payments**  
A WebWallet recharge is interpreted as a purchase intention, therefore it cannot be reversed. The balance loaded on a WebWallet cannot be redeemed for cash, so it must be spent on a purchase at some point.


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
  - If a transaction token expires, the initiator party will need to request a new one and start over.  

## API Overview
The WebWallet API defines how to expose and interact with WebWallet instances in order to request and carry out transactions.

* **Exposure through URIs**  
Each WebWallet is represented as a URI:  

    ```
    wallet.playbanq.com/:walletID
    ```

* **Secure API calls using tokens**  
Transactions are authorized by requesting and obtaining a transaction token:

    ```
    POST /:walletID/transactions HTTP/1.1
    Host: wallet.playbanq.com
    
    options=transaction-options
    ```

* **Transactions through HTTP methods**  
Transactions are carried out using HTTP methods signed with a valid transaction token.

    **Checking the balance:**
    ```
    GET /:walletID/balance HTTP/1.1
        ?token=transaction-token
    Host: wallet.playbanq.com
    ```

    **Adding funds:**
    ```
    POST /:walletID/funds HTTP/1.1
    Host: wallet.playbanq.com
    
    token=transaction-token
    ```
    
    **Sending/Requesting a payment:**
    ```
    POST /:walletID/payments HTTP/1.1
    Host: wallet.playbanq.com
    
    token=transaction-token
    ```
