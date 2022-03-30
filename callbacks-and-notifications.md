---
title: "Callbacks and notifications"
slug: "callbacks-and-notifications"
excerpt: "Follow step by step"
hidden: false
createdAt: "2019-06-06T20:51:21.623Z"
updatedAt: "2019-06-28T10:10:51.068Z"
---
[block:api-header]
{
  "title": "Callbacks"
}
[/block]
GotoCrypto will notify your service about payment status. All callbacks will be sent to the URL specified on your GotoCrypto merchant page as POST request with the following body:
[block:code]
{
  "codes": [
    {
      "code": "{\n \t\"MerchantOrderId\":\"7\",\n \t\"BuyerEmail\":\"user@example.com\",\n \t\"Sum\":\"99.99\",\n \t\"Currency\":\"USD\",\n\t\"Hash\":\"3D3816...9333D1\",\n  \t\"Status\":\"PAID\"\n}",
      "language": "json"
    }
  ]
}
[/block]
- **MerchantOrderId** - is an order identifier on your side that you send when an order is created.
- **BuyerEmail** - buyer's email address that was sent at the moment the order was created.
- **Sum** - total order price
- **Currency** - order's currency. USD is the only available option for now.
- **Hash** - hash sum for verification. The calculating algorithm is described below.
- **Status** - current order's status.

##Statuses
There are two possible statuses:
- **MINING** - means that the customer has sent full order price to the buffer wallet, but this transaction HAS NOT BEEN CONFIRMED by blockchain yet. Therefore, the specified order should NOT be considered as paid. You can use this message to notify your customer that his payment is already in progress. 
- **PAID** - means the payment transaction is already irreversible. After receiving this message you can consider an order as paid and send goods to your customer.

###Hash
We are using the strong hash algorithm **SHA512** of the following string: 

- **MerchantOrderId + Sum + PassPhrase**

Where **"PassPhrase"** is a secret part that is specified on your settings page

e.g order identifier is **7**, price is **$99.99**, PassPhrase is **"secret"**
So **SHA512(799.99secret) == "71FC69...F63A"**

*Notice*: The string converted to hex before hashing and the result is upper case.
*IMPORTANT*: Consider callback verified only if the hash is 100%, THE SAME as calculated on your side.

##Response
Set your service to immediately respond to any GotoCrypto callback with **200** code (**OK**). Otherwise callbacks will repeat approximately every 10 minutes. 
All response content except code will be ignored.