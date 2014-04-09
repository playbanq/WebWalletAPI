#The WebWallet API
###A digital wallet specification for the Web.

## Abstract
This specification defines an API that aims to enable Web applications to carry out electronic commerce transactions using WebWallets.

## What is a WebWallet?
A WebWallet is a digital wallet that can temporarily store money on the Web for intended online purchases.

+ **No deposits, but recharges**  
A WebWallet is funded through prepaid recharges, and funds can be added in any currency supported by the target merchant and the applicable regulations.

+ **No withdrawals, only payments**  
A WebWallet recharge is interpreted as a purchase intention, therefore it cannot be reversed. The balance loaded on a WebWallet cannot be redeemed for cash, so it must be spent on a purchase at some point.


## API Overview
The WebWallet API defines how to expose and interact with WebWallet instances in order to request and carry out transactions.

* **Exposure through URLs**  
Each WebWallet is represented as a URL:  

    ```
    wallet.playbanq.com/:walletID
    ```

* **Transactions through HTTP methods**  
Transactions are carried out using HTTP methods with the proper credentials.
    
    **Checking the available balance:**
    ```
    GET /:walletID/balance
    Host: wallet.playbanq.com
    ```

    **Adding funds:**
    ```
    POST /:walletID/funds
    Host: wallet.playbanq.com
    
    ammount=some-ammount
    currency=some-currency
    ```
    
    
    **Requesting funds for a payment:**
    ```
    GET /:walletID/funds/:ammount/:currency
    Host: wallet.playbanq.com
    ```
