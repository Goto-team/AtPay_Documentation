# AtPay payment system public documentation

AtPay payment system gives your buyers a simplified and secure checkout experience with cryptocurrencies. The person who clicks the payment button is redirected from the merchant site to the AtPay Checkout and then back to the merchant site.

## How to start?

Now we are working on production in alpha testing mode. If you want to try to accept crypto payments, send us an email on [at@atpay.online](mailto:at@atpay.online). Please send us all your business documents and owner's or CEO’s ID to comply with the KYC-AML procedure and signing agreement.
We will register you in the AtPay payment system, set a `pass_phrase` and issue a `merchant_id`.
After that, you can start using our payment system.

## How to process payment?

Now let's look at the process of creating, paying and confirming an order step by step.

### Step 1. Create order

First you need to create an order in the AtPay payment system by executing a REST API request. Upon successful completion, the url for the buyers redirect will be returned.

- API endpoint `https://processing.atpay.online/api/order`
- Method `POST`
- Content type `application/json`

#### Request body example

```json
{
    "merchant_id": "2938", // REQUIRED Merchant ID in AtPay
    "hash": "5EC6024791AF6C394A903E237578E78B1EDE34DD228F86386673DE8B34523DB72C42610DC11454AE510B14FBBF3142B3C5D74C5FB00865DE8794C487129E50E4", // REQUIRED
    "type": "onetime", // REQUIRED Payment type
    "currency_code": "USD", // REQUIRED Order currency
    "price": "579.56", // REQUIRED Total cost of the order
    "merchant_order_id": "952417836", // REQUIRED Merchant's order number
    "subtotal": "579.56", // REQUIRED
    "tax": "20.82", // Tax on the whole order

    "success_url": "https://avengers.com/success",  // REQUIRED Address for redirecting the buyer after successful payment
    "cancel_url": "https://avengers.com/cancel", // REQUIRED Address for redirecting the buyer in case of cancellation or unsuccessful payment
    "custom": "[{'requestId: '2908734'},{ 'customToken': 'slkan29837'}]", // Bypass field to pass directly to "notify_url"

    "customer": { // Buyer information
        "email": "spiderman@avengers.com", // REQUIRED
        "first_name": "Peter",
        "last_name": "Parker",
        "country": "USA",
        "zip": "658045",
        "state": "NY",
        "city": "NY",
        "address1": "20 Ingram Street",
        "address2": ""
    },

    "items": [ // Array of products
        {
            "name": "Web Shooter", // REQUIRED Product name
            "price": "58.12", // REQUIRED Product price
            "tax": "2.34", // Product tax
            "number": "893468", // Product SKU
            "amount": "3", // Quantity of product
            "description": "The Web-Shooters are a pair of wrist-mounted mechanical devices" // Product description
        }
    ]
}
```

The `hash` field is calculated as follows: SHA512(merchant_id + pass_phrase), and then cast to uppercase.
For example, if `merchant_id` == 1 and `pass_phrase` == "secret", then the hash will be:
`5EC6024791AF6C394A903E237578E78B1EDE34DD228F86386673DE8B34523DB72C42610DC11454AE510B14FBBF3142B3C5D74C5FB00865DE8794C487129E50E4`

#### Response body example

```json
{
    "orderId": "08d73097-1365-779a-6e65-ae1198565cbc", // AtPay internal order number
    "merchantOrderId": "952417836", // Merchant ID in AtPay
    "created": "2022-03-11T12:10:50.0000000", // Date the order was created in AtPay
    "price": "579.56",
    "paymentUrl": "https://checkout.atpay.online/?id=08d73097-1365-779a-6e65-ae1198565cbc" // AtPay Checkout url to redirect the user to it
}
```

#### Possible errors

- `merchant not found`
- `wrong hash`
- `order already exists` - if an order with this `merchant_order_id` is already registered

### Step 2. Redirect buyer to pay order

At this stage, the buyer should be redirected to the AtPay Checkout payment application to pay for the order.
You can simply redirect the buyer to the address obtained from the `paymentUrl` parameter immediately after the previous request is completed.
Or you can create a payment button so that the buyer can choose when to pay.

#### Pay button

To get started you need to create an HTML form from scratch with orderId value.
The `form` tag includes two required attributes, `action` and `method`. Do not change these values, these attributes are required for payment button.
The `input` tag includes three required attributes, `name` and `value`. AtPay internal order number should be filled in `value` field with "id" filled in `name` field. `type` field should be hidden.

Example button code:

```html
<form action="https://checkout.atpay.online/" method="get">
    <input type="hidden" name="id" value="08d73097-1365-779a-6e65-ae1198565cbc">
    <button type="submit">Pay with AtPay</button>
</form>
```

#### Pay process

1. The buyer is redirected to the AtPay Checkout payment application with the internal order number.
2. Internal order number is sent to the AtPay server to verify and obtain information about the order.
3. The order is temporarily stored in the local storage of the buyer's browser.
4. The buyer can fill/edit the email field, agrees to the terms of use of the application, and press the payment button with one of the cryptocurrencies.
5. The buyer is shown the amount to pay for the order in the selected cryptocurrency and the address where the buyer needs to send money. Additionally, the QR code with the address for payment is displayed.
6. Next, AtPay Checkout application waits for the transfer of the required amount of money to the issued crypto-currency address by monitoring the blockchain.
7. As soon as the AtPay server records the receipt of funds, the buyer will be shown a notice of successful payment. This is only a preliminary notice to the buyer. Full confirmation usually takes several tens of minutes.
8. After a 10 second timeout, the buyer will return to the merchant site using the link filled in the `success_url` field when creating order.
9. Further, the merchant site should expect to be informed about the full payment of the order from the AtPay server to the specified URL filled in the AtPay server.

### Step 3. Receipt of payment confirmation

More on this process will be written later...
