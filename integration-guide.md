---
title: "Checkout form"
slug: "integration-guide"
excerpt: "Follow step by step."
hidden: false
createdAt: "2019-06-04T00:35:24.789Z"
updatedAt: "2019-06-28T09:45:42.736Z"
---
[block:api-header]
{
  "title": "Checkout Application"
}
[/block]
GotoCrypto Checkout gives your buyers a simplified and secure checkout experience with cryptocurrencies. When customers click the payment button, they are redirected from the merchant site to the GotoCrypto Checkout, and then back to the merchant site.
[block:api-header]
{
  "title": "Pay button"
}
[/block]
People who pay you through GotoCrypto Checkout interact with HTML forms and hidden HTML input variables that you place on your website. When someone clicks a payment button in an HTML form on a webpage, the form submits the variables and their values to GotoCrypto Checkout. You set the values of the variables to produce the desired effect. A few examples of variables include the order number, cost, customer email and more.
[block:api-header]
{
  "title": "Form"
}
[/block]
To get started, you need to create an HTML form from scratch with desired variables. The form tag includes two required attributes, action and method, which should look like this:
[block:code]
{
  "codes": [
    {
      "code": "<form action=\"https://go2crypto.express\" method=\"get\">\n    // Variables\n</form>",
      "language": "html"
    }
  ]
}
[/block]
Do not change these values. These attributes are required for the payment button.

### Allowed variables:

- **id** (*Required*) - Merchant ID in GotoCrypto
- **OrderId** (*Required*) - Order number in merchant's shop
- **Price** (*Required*) - Order value in U.S. dollars
- **Email** - Buyer Email

The variable marked as "(*Required*)" means that if this field is left blank or completed incorrectly, the customer will not be able to pay for the order and see an error message. Then they will be redirected to the merchant site.

Optional variables, such as email, may be missing. To pay for the order, the buyer will need to fill in the email field in the application. The buyer can always change the email field in the application.

The value of each variable should be filled in the `value` field with name of variable filled in `name`. The `type` field should be hidden. For example:
[block:code]
{
  "codes": [
    {
      "code": "<input type=\"hidden\" name=\"Id\" value=\"1\">",
      "language": "html"
    }
  ]
}
[/block]
## Example of button code

The following sample HTML code shows a payment button with variables for automatically filling out GotoCrypto Checkout forms for the customer. Your website generates the field entries dynamically from information that your website gathers about the customer. The variables and their values are included in the URL to which customers are sent when they click the payment button.
[block:code]
{
  "codes": [
    {
      "code": "<form action=\"https://go2crypto.express\" method=\"get\">\n    <input type=\"hidden\" name=\"Id\" value=\"1\">\n    <input type=\"hidden\" name=\"OrderId\" value=\"00534\">\n    <input type=\"hidden\" name=\"Price\" value=\"54,25\">\n    <button type=\"submit\">Pay with GotoCrypto</button>\n</form>",
      "language": "html"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Payment process"
}
[/block]
1. Checks for the presence of all required parameters.
2. Merchant ID is sent to the GotoCrypto server to verify and obtain information about the seller.
3. The order is temporarily stored in the local storage of the buyer's browser.
4. The customer can complete/edit the email field, agree to the terms of use of the application, and create an order in the GotoCrypto server by pressing the payment button with one of the cryptocurrencies.
5. The customer is shown the amount to pay for the order in the selected cryptocurrency with the address where the customer needs to send money. The QR code, with the address for payment, is displayed.
6. Next, the GotoCrypto Checkout application waits for the transfer of the required amount of money to the issued cryptocurrency address by monitoring the blockchain.
7. As soon as the GotoCrypto server records the receipt of funds, the buyer will be shown a notice of successful payment. This is only a preliminary notice to the buyer. Full confirmation usually takes several minutes.
8. After about 10 seconds, the buyer will return to the merchant site using the link filled in the GotoCrypto server.
9. The merchant site should expect to be informed about the full payment of the order from the GotoCrypto server to the specified URL filled in the GotoCrypto server. More on this process will be written later...