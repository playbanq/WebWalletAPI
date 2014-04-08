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

+ **Exposure through URLs**  
Each WebWallet is represented as a URL, and transactions can be requested and carried out using HTTP methods with the proper credentials.
