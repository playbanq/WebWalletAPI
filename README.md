#The WebWallet API <span>&nbsp;&nbsp;(working draft)</span>
###A digital wallet specification for the Web.

## Abstract
This specification defines an application programming interface (API) that aims to enable web applications to carry out electronic commerce transactions using WebWallets.

## What is a WebWallet?
A WebWallet is a rechargeable digital wallet that can hold electronic money and credentials to make payments on the Web.

#### Features
+ **No deposits but recharges**  
A WebWallet is not a deposit account, but a rechargeable digital wallet. Funds are not added as deposits but as prepaid recharges, which represent a temporary intermediation between online buyers and sellers.

+ **No withdrawals or redemption**  
A WebWallet recharge is interpreted as a purchase intention, so it cannot be reversed. The funds added to a WebWallet cannot be withdrawn or redeemed for cash, so they must be fully spent at some point.

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


## WebWallet Modules
The WebWallet specification is divided into modules that resemble the different pockets of a physical wallet intended for holding paper money, identification documents, cards and receipts, among others.

+ **Electronic Money**  
  The main goal of a WebWallet is to be as easy to use as a physical wallet: put some money on it, and take some money out to make payments.  

  In the context of digital wallets, money is referred to as electronic money, and funds are added against equal value of fiat currency on recharges. Electronic money must not be confused with virtual currencies, since the latter do not have fiat currency counterparts.  
  
  _"Electronic money is a digital equivalent of cash, stored on an electronic device or remotely at a server."_ [_(European Comission on E-money)_](http://ec.europa.eu/internal_market/payments/emoney/index_en.htm). _"E-money is a mechanism for interacting with government-issued and regulated currencies"_ [_(CGAP.org)_](http://www.cgap.org/news/bitcoin-vs-electronic-money-digital-different).

  The purpose of the Electronic Money module is to resemble the pocket of a physical wallet that is used for holding paper money, by implementing a digital stored value system that reflects the amount of fiat currency recharged and deducts the value spent or hold for payments.


## API Overview
The WebWallet API defines how to expose and interact with WebWallets in order to request and carry out electronic commerce transactions.

+ **Exposure through URIs**  
Each WebWallet is represented as a URI:  

    ```
    wallet.example.com/:walletID
    ```

+ **Secure API calls using tokens**  
A transaction token must be requested in order to obtain authorization to make API calls:

    ```
    POST /:walletID/transactions HTTP/1.1
    Host: wallet.example.com
    
    options=transaction-options
    ```

+ **Transactions through HTTP methods**  
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

## WebWallet Transaction Flow

#### Payments
WebWallet payments come in two flavors: user initiated and merchant initiated.

+ **User initiated: merchant charges**  
  When a user initiates a payment transaction, the merchant has to complete it by charging the payment:  

  **1.** The user creates a transaction by requesting a transaction token with the payment information.  
    ```
    POST /:userWalletID/transactions HTTP/1.1
    Host: wallet.example.com
    
    options=transaction-options
    ```  
  **2.** The user passes the transaction token to the merchant as an authorization to request the payment.  
    ```
    POST /:merchantWalletID/tokens HTTP/1.1
    Host: wallet.example.com
    
    token=transaction-token
    ```  
  **3.** The merchant completes the transaction by posting the payment request using the transaction token.  
    ```
    POST /:userWalletID/payments HTTP/1.1
    Host: wallet.example.com
    
    token=transaction-token
    ```  
  
  By creating and handing over a payment transaction token to a merchant, a user authorizes the merchant to charge the payment to the user's wallet. This is similar to direct debit and credit card charges that are authorized by subscribing recurring payments, swiping a card or filling an online form with its details.

+ **Merchant initiated: user pays**  
  When a merchant initiates a payment transaction, the user has to complete it by sending the payment:  

  **1.** The merchant creates a transaction by requesting a transaction token with the payment information.  
    ```
    POST /:merchantWalletID/transactions HTTP/1.1
    Host: wallet.example.com
    
    options=transaction-options
    ```  
  **2.** The merchant passes the transaction token to the user as a request or demand to make a payment.  
    ```
    POST /:userWalletID/tokens HTTP/1.1
    Host: wallet.example.com
    
    token=transaction-token
    ```  
  **3.** The user completes the transaction by posting the payment using the transaction token.  
    ```
    POST /:merchantWalletID/payments HTTP/1.1
    Host: wallet.example.com
    
    token=transaction-token
    ```  

  By creating and handing over a payment transaction token to a user, a merchant requests or demands the user to send the payment to the merchant's wallet. This is similar to cash transactions in which a merchant communicates the payment ammount (e.g. invoice) and waits for the user to hand over the money.
