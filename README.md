#The WebWallet API
###A digital wallet specification for the Web.

## Abstract
This specification defines a REST API that aims to enable web applications to carry out electronic commerce transactions using WebWallets.

## What is a WebWallet?
A WebWallet is a digital wallet that can store credentials and carry out electronic commerce transactions on the Web.

- **Features and constraints:**  

  **1. Each WebWallet is identified by a URL.**  
  
  ```
  https://wallet.playbanq.com/:walletID
  ```
      
  **2. Transactions are carried out with HTTP methods.**  
  
  ```
  GET    POST
  ```

  **3. WebWallets are exposed through a REST API.**  
  
  ```
  Uniform interface, though not cacheable.
  ```
 
## WebWallet IDs
  - **Explicit IDs**  
  An explicit WebWallet ID can be formed by concatenating a wallet namespace (e.g. ISO 3166 country code), a publicly verifiable identification number (e.g. mobile number) and a currency (e.g. ISO 4217 currency code) with colon (':') characters:

  ```
  walletID = walletNamespace:walletNumber:currencyCode
                 countryCode:mobileNumber:currencyCode
  
  us:8301234567:USD    be:412345678:EUR     cn:13123456789:CNY    ru:9123456789:RUB
  co:3123456789:COP    gb:7123456789:GBP    jp:01234567890:JPY    in:9123456789:INR
  br:912345678:BRL     za:7123456789:ZAR    kr:1012345678:KRW     ae:512345678:AED     
  ```

  - **Opaque IDs**  
  An opaque WebWallet ID can be formed by base58 encoding the SHA-1 hash of the explicit ID to obtain a URL-safe 28 alphanumeric character identifier.  

  ```
  explicit: walletNamespace:walletNumber:currencyCode
  SHA-1:    c7305dbdebd931cf6550950a292d55126f067bd6
  
  base58:   3mxDwVyhMwwW4WTbGuTn3NaRW2Hj
  ```
  
## WebWallet Tokens
The majority of WebWallet API calls need to include a bearer token in the HTTP Authorization header in order to provide temporary, secure access to a specific set of resources (i.e. wallets) and operations (i.e. transactions). 

#### JSON Web Tokens (JWTs)  

  WebWallet tokens follow the JSON Web Token format, a compact URL-safe means of representing claims to be transferred between two parties, which are encoded as a JSON object that is used as the payload of a JSON Web Signature (JWS) structure and digitally signed or MACed and/or encrypted.
  
  Before being encoded, a WebWallet token should have the following structure:  
  
``` js
{
  "header": {
     "alg": "a signature algorithm"
  },
  "payload": {
     "claim": "content"
     ...
  },
  "signature": "a cryptographic signature"
}
```

  After being encoded, a WebWallet token should have the following dot-separated structure:
  
```
base64urlEncodedHeader.base64urlEncodedPayload.base64urlEncodedCryptographicSignature
```

#### Types of Tokens  

   - **Access Tokens**  
     Access tokens are credentials used to access protected resources. They enable a third-party application to obtain limited access to a particular set of resources and operations, either on behalf of a resource owner or on its own behalf. Access tokens can be obtained by making calls to the appropriate API endpoint according to the IETF's RFC 6749 Oauth 2.0 specification.
   - **Refresh Tokens**  
     Refresh tokens are credentials used to obtain access tokens. They enable a third-party application to obtain a new access token when the current access token expires or becomes invalid. A refresh token will be issued along with every access token that is issued for a third-party application and it will remain valid until a new refresh token is requested.
   - **Transaction Tokens**  
     Transaction tokens are credentials used to express proof of consent. They enable a resource owner to hand over a proof of consent to another resource owner in order to engage in a particular transaction. Transaction tokens contain information such as the intended subject, audience and the attributes of the transaction that will be carried out if the token is used. 

## API Overview
The WebWallet API defines how to expose and interact with WebWallets in order to request and carry out electronic commerce transactions.

+ **/oauth/authorize**  
  This endpoint allows applications to obtain access tokens for the implicit and authorization code grant types according to the IETF's RFC 6749 Oauth 2.0 specification.

  ```
  GET /oauth/authorize HTTP/1.1
      
  Host: wallet.playbanq.com
  ```
   
  - **Implicit grant type:**  
    Browser-based applications can use this flow to get an access token without using a server. The WebWallet owner will be prompted to login if necessary and to accept or decline the authorization request. Finally the browser will be redirected to the specified URI with the authorization response in the hash fragment.

    GET request structure:
    ```
    GET wallet.playbanq.com/oauth/authorize HTTP/1.1
        ?response_type=token
        &client_id=CLIENT_ID
        &redirect_uri=REDIRECT_URI
        &scope=charges,refills,refunds
    ```
    
    Successful GET response:
    ```
    http(s)://REDIRECT_URI#token=ACCESS_TOKEN
    ```
    
    Unsuccessful GET response:
    ``` 
    http(s)://REDIRECT_URI#error=ACCESS_DENIED
    ```
    
  - **Authorization code grant type (part 1):**  
    Server-based applications can use this flow to get an authorization code and then exchange it for an access token by making a call to the **/oauth/token** endpoint. The WebWallet owner will be prompted to login if necessary and to accept or decline the authorization request. Finally the user agent will be redirected to the specified URI with the authorization response as a query parameter.
  
    GET request structure:
    ```
    GET wallet.playbanq.com/oauth/authorize HTTP/1.1
        ?response_type=code
        &client_id=CLIENT_ID
        &redirect_uri=REDIRECT_URI
        &scope=charges,refills,refunds
    ```
    
    Successful GET response:
    ```
    http(s)://REDIRECT_URI?code=AUTHORIZATION_CODE
    ```
    
    Unsuccessful GET response:
    ``` 
    http(s)://REDIRECT_URI?error=AUTHORIZATION_ERROR
    ```
    
    After obtaining an authorization code, an application will be able to exchange it for an access token as shown in **/oauth/token** Authorization code grant type (part 2).

+ **/oauth/token**  
  This endpoint allows applications to obtain access tokens for the client credentials grant type according to the IETF's RFC 6749 Oauth 2.0 specification.

  ```
  POST /oauth/token HTTP/1.1

  Host: wallet.playbanq.com
  ```
  
  - **Client credentials grant type:**  
    POST request structure:
    ``` js
    {
      "grant_type": "client_credentials",
      "client_id": <client_id>,
      "client_secret": <client_secret>
    }
    ```
    
    Successful POST response:
    ``` js
    {
      "access_token": <ACCESS_TOKEN>,
      "token_type": "Bearer",
      "expires_in": <numberOfSeconds>,
      "refresh_token": <REFRESH_TOKEN>
    }
    ```
    
  - **Authorization code grant type (part 2):**  
    POST request structure:
    ``` js
    {
      "grant_type": "authorization_code",
      "code": <authorization_code>,
      "redirect_uri": <redirect_uri>,
      "client_id": <client_id>,
      "client_secret": <client_secret>
    }
    ```
    
    Successful POST response:
    ``` js
    {
      "access_token": <ACCESS_TOKEN>,
      "token_type": "Bearer",
      "expires_in": <numberOfSeconds>,
      "refresh_token": <REFRESH_TOKEN>
    }
    ```

+ **/:walletID**  
  **/:walletNamespace/:walletNumber**  
  ```
  GET /:walletID HTTP/1.1
      /:walletNamespace/:walletNumber HTTP/1.1

  Host: wallet.playbanq.com
  Authorization: Bearer <TOKEN>
  ```
  
  -  **/balance**  
    This endpoint allows applications to check the balance of a WebWallet.

    ```
    GET /:walletID/balance HTTP/1.1
        /:walletNamespace/:walletNumber/balance HTTP/1.1

    Host: wallet.playbanq.com
    Authorization: Bearer <TOKEN>
    ```
    
    Successful GET response:
    ``` js
    {
      "payload": {
         "balance" : {
            "amount": <number>,
            "currency": <currencyCode>,
            "limits": {
               lower: <number>,
               upper: <number>
            },
         }
      }
    }
    ```
    
 - **/charges**  
    This endpoint allows applications to make charges to a WebWallet.
    
    ```
    POST /:walletID/charges HTTP/1.1
         /:walletNamespace/:walletNumber/charges HTTP/1.1

    Host: wallet.playbanq.com
    Authorization: Bearer <TOKEN>
    ```
    
    POST request structure:
    ``` js
    {
      "header": {
         "alg": "hs256"
      },
      "payload": {
         "endpoint": "charges"
         "target": <walletID>,
         "amount": <totalAmount>,
         "currency": <currencyCode>,
         // optional:
         "details": {
            "subtotal": <subtotalAmount>,
            "taxes": <taxAmount>,
            "fees": <feeAmount>,
            "other": <otherAmount>
         },
         "description": <chargeDescription>,
         "metadata": {chargeMetadata}
      },
      "signature": <hs256(payload)>
    }
    ```
    
    Successful POST response:
    ``` js
    {
      "payload": {
         "endpoint": "charges",
         "target": <walletID>,
         "amount": <totalAmount>,
         "currency": <currencyCode>,
         "details": {
            "subtotal": <subtotalAmount>,
            "taxes": <taxAmount>,
            "fees": <feeAmount>,
            "other": <otherAmount>
         },
         "description": <chargeDescription>,
         "metadata": {chargeMetadata},
         "timestamp": <creationTime>,
         "reference": <string>,
         "success": true
      }
    }
    ```
    
  - **/payments**  
    This endpoint allows applications to send a payment to a WebWallet.

    ```
    POST /:walletID/payments HTTP/1.1
         /:walletNamespace/:walletNumber/payments HTTP/1.1

    Host: wallet.playbanq.com
    Authorization: Bearer <TOKEN>
    ```
    
    POST request structure:
    ``` js
    {
      "header": {
         "alg": "hs256"
      },
      "payload": {
         "endpoint": "payments"
         "target": <walletID>,
         "amount": <totalAmount>,
         "currency": <currencyCode>,
         // optional:
         "details": {
            "subtotal": <subtotalAmount>,
            "taxes": <taxAmount>,
            "fees": <feeAmount>,
            "other": <otherAmount>
         },
         "description": <paymentDescription>,
         "metadata": {paymentMetadata}
      },
      "signature": <hs256(payload)>
    }
    ```
    
    Successful POST response:
    ``` js
    {
      "payload": {
         "endpoint": "payments",
         "target": <walletID>,
         "amount": <totalAmount>,
         "currency": <currencyCode>,
         "details": {
            "subtotal": <subtotalAmount>,
            "taxes": <taxAmount>,
            "fees": <feeAmount>,
            "other": <otherAmount>
         },
         "description": <paymentDescription>,
         "metadata": {paymentMetadata},
         "timestamp": <creationTime>,
         "reference": <string>,
         "success": true
      }
    }
    ```
    
  - **/invoices**  
    This endpoint allows applications to send an invoice to a WebWallet.
    ```
    POST /:walletID/invoices HTTP/1.1
         /:walletNamespace/:walletNumber/invoices HTTP/1.1

    Host: wallet.playbanq.com
    Authorization: Bearer <TOKEN>
    ```
    
    POST request structure:
    ``` js
    {
      "header": {
         "alg": "hs256"
      },
      "payload": {
         "endpoint": "invoices",
         "target": <walletID>,
         "amount": <totalAmount>,
         "currency": <currencyCode>,
         // optional:
         "details": {
            "subtotal": <subtotalAmount>,
            "taxes": <taxAmount>,
            "fees": <feeAmount>,
            "other": <otherAmount>
         },
         "description": <invoiceDescription>,
         "metadata": {invoiceMetadata}
      },
      "signature": <hs256(payload)>
    }
    ```
    
    Successful POST response:
    ``` js
    {
      "payload": {
         "endpoint": "invoices",
         "target": <walletID>,
         "amount": <totalAmount>,
         "currency": <currencyCode>,
         "details": {
            "subtotal": <subtotalAmount>,
            "taxes": <taxAmount>,
            "fees": <feeAmount>,
            "other": <otherAmount>
         },
         "description": <invoiceDescription>,
         "metadata": {invoiceMetadata},
         "timestamp": <creationTime>,
         "reference": <string>,
         "success": true
      }
    }
    ```
    
  - **/refills**  
    This endpoint allows applications to request refills for a WebWallet from its parent WebWallet.
    
    ```
    POST /:walletID/refills HTTP/1.1
         /:walletNamespace/:walletNumber/refills HTTP/1.1

    Host: wallet.playbanq.com
    Authorization: Bearer <TOKEN>
    ```
    
    POST request structure:
    ``` js
    {
      "header": {
         "alg": "hs256"
      },
      "payload": {
         "endpoint": "refills",
         "target": <walletID>,
         "amount": <refillAmount>,
         "currency": <currencyCode>
      },
      "signature": <hs256(payload)>
    }
    ```
    
    Successful POST response:
    ``` js
    {
      "payload": {
         "endpoint": "refills",
         "target": <walletID>,
         "amount": <refillAmount>,
         "currency": <currencyCode>,
         "timestamp": <refillTime>,
         "reference": <string>,
         "success": true
      }
    }
    ```

  - **/refunds**  
    This endpoint allows applications to send refunds to a WebWallet.
    
    ```
    POST /:walletID/refunds HTTP/1.1
         /:walletNamespace/:walletNumber/refunds HTTP/1.1

    Host: wallet.playbanq.com
    Authorization: Bearer <TOKEN>
    ```
    
    POST request structure:
    ``` js
    {
      "header": {
         "alg": "hs256"
      },
      "payload": {
         "endpoint": "refunds",
         "target": <walletID>,
         "amount": <refundAmount>,
         "currency": <currencyCode>,
         "transaction": <transactionToRefund>,
         "description": <refundDescription>
      },
      "signature": <hs256(payload)>
    }
    ```
    
    Successful POST response:
    ``` js
    {
      "payload": {
         "endpoint": "refunds",
         "target": <walletID>,
         "amount": <refundAmount>,
         "currency": <currencyCode>,
         "transaction": <transactionToRefund>,
         "description": <refundDescription>,
         "timestamp": <refundTime>,
         "reference": <string>,
         "success": true
      }
    }
    ```

#License and Copyright
© Playbanq SAS
