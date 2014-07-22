#The WebWallet API
###A digital wallet specification for the Web.

## Abstract
This specification defines a REST API that aims to enable web applications to carry out electronic commerce transactions using WebWallets.

## What is a WebWallet?
A WebWallet is a digital wallet that can store credentials and carry out electronic commerce transactions on the Web.

- **Features and constraints:**  

  **1. Each WebWallet is identified by a URL.**  
  
  ```
  https://wallet.example.com/:walletID
  ```
      
  **2. Transactions are carried out with HTTP methods.**  
  
  ```
  GET    POST
  ```

  **3. WebWallets are exposed through a REST API.**  
  
  ```
  Uniform interface, though not cacheable.
  ```
      
## API Overview
The WebWallet API defines how to expose and interact with WebWallets in order to request and carry out electronic commerce transactions.

+ **/:walletID**  
  ```
  GET /:walletID HTTP/1.1

  Host: wallet.example.com
  Authorization: Bearer TOKEN
  ```
  
  -  **/balance**  
    ```
    GET /:walletID/balance HTTP/1.1

    Host: wallet.example.com
    Authorization: Bearer TOKEN
    ```

  - **/funds**  
    ```
    POST /:walletID/funds HTTP/1.1

    Host: wallet.example.com
    Authorization: Bearer TOKEN
    ```
    
  - **/payments**  
    ```
    POST /:walletID/payments HTTP/1.1

    Host: wallet.example.com
    Authorization: Bearer TOKEN
    ```

  - **/charges**  
    ```
    POST /:walletID/charges HTTP/1.1

    Host: wallet.example.com
    Authorization: Bearer TOKEN
    ```

  - **/transactions**  
    ```
    POST /:walletID/transactions HTTP/1.1

    Host: wallet.example.com
    Authorization: Bearer TOKEN
    ```
    
    ```
    GET /:walletID/transactions HTTP/1.1

    Host: wallet.example.com
    Authorization: Bearer TOKEN
    ```
    
## WebWallet Tokens
The majority of WebWallet API calls need to include a bearer token in order to provide temporary, secure access to a specific set of resources (i.e. wallets) and operations (i.e. transactions). 

#### JSON Web Tokens (JWTs)  

  WebWallet tokens follow the JSON Web Token format, a compact URL-safe means of representing claims to be transferred between two parties, which are encoded as a JSON object that is used as the payload of a JSON Web Signature (JWS) structure and digitally signed or MACed and/or encrypted.
  
  Before being encoded, a WebWallet token should have the following structure:  
  
```
{
  header: { alg: 'a signature algorithm' },
  payload: {
    claim: 'content'
    ...
  },
  signature: 'a cryptographic signature'
}
```

  After being encoded, a WebWallet token should have the following dot-separated structure:
  
```
base64urlEncodedHeader.base64urlEncodedPayload.base64urlEncodedCryptographicSignature
```

#### Types of Tokens

  - **Access Tokens**

  - **Transaction Tokens**


## WebWallet IDs
  - **Explicit IDs**  
  An explicit WebWallet ID can be formed by concatenating a country code, a publicly verifiable identification number (e.g. mobile number) and a currency code with colon (':') characters:

  ```
  countryCode:mobileNumber:currencyCode
  
  us:8301234567:USD     cn:13123456789:CNY     be:412345678:EUR      ru:9123456789:RUB
  co:3123456789:COP     jp:01234567890:JPY     gb:7123456789:GBP     in:9123456789:INR
  br:912345678:BRL      kr:1012345678:KRW      za:7123456789:ZAR     ae:512345678:AED     
  ```

  - **Opaque IDs**  
  An opaque WebWallet ID can be formed by base58 encoding the SHA-1 hash of the explicit ID to obtain a URL-safe 28 alphanumeric character identifier.  

  ```
  explicit: countryCode:mobileNumber:currencyCode
  SHA-1:    7c67ade76eeccb0fa960c2391c9e07d8cb7072d6
  
  base58:   2jXPQQRP2dbDtV6MXRQbwkzrLPL9
  ```
 
