---
title: "Order processing example"
slug: "order-processing-description"
hidden: false
createdAt: "2019-06-06T19:44:30.607Z"
updatedAt: "2019-06-28T10:13:25.766Z"
---
Here is an example of how order proccessing can look on a merchant account to make the integration proccess easier. 

The payment system notifies the merchant data using the POST method. Here is an example of data structure which comes from the payment system:
[block:code]
{
  "codes": [
    {
      "code": "{\n\t\"MerchantOrderId\":\"2\",\n\t\"BuyerEmail\":\"user@example.com\",\n\t\"Sum\":\"1\",\n\t\"Currency\":\"USD\",\n  \t\"Hash\":\"C229255...A1E7C75\",\n\t\"Status\": \"MINING\"\n}",
      "language": "json"
    }
  ]
}
[/block]
The system sends 2 types of statuses:
- **“MINING”** means that we see a transaction on blockchain and are waiting for its confirmation. This is NOT subject to starting delivery of goods. Waiting for "PAID" status.
- **“PAID”**  means that we have enough confirmations on blockchain and you can deliver your goods.

First we need to find an order. It could look like this:
[block:code]
{
  "codes": [
    {
      "code": "$order = Order::with('products')\n ->where('id', $data['MerchantOrderId'])\n ->get();\nif(!$order>isEmpty()) {}",
      "language": "php"
    }
  ]
}
[/block]
After an order has been found, its HASH integrity should be verified.
Code example:
[block:code]
{
  "codes": [
    {
      "code": "$hash = strtoupper(hash('sha512', $order->id . $order->products->sum('price') . secret_key_of_pay_app, false));\nif($hash === $data['Hash']){}",
      "language": "php"
    }
  ]
}
[/block]
- **OrderId** - order ID in the store;
- **ProductsSum** - Order amount;
- **secret_key_of_pay_app** - This key will be recieved by merchant after registration in GotoCrypto system.

Next, we need to determine what status came from payment gateway:
[block:code]
{
  "codes": [
    {
      "code": "switch ($data['Status']){\n case 'PAID':\n break;\n case 'MINING':\n break;\n}",
      "language": "php"
    }
  ]
}
[/block]
Depending on the status that came to the merchant from the payment system, we perform the required order manipulations.
Example code for the status “MINING”: Check the status of the order. If the order is not equal to the status of pending payment. Then we ignore it and send an error message stating that there is no such order.
[block:code]
{
  "codes": [
    {
      "code": "if ($order->type_price !== 'AWAIT_PAID'){}",
      "language": "php"
    }
  ]
}
[/block]
Then, if the order is found and it satisfies us in all parameters, we place all the ordered goods on hold.
[block:code]
{
  "codes": [
    {
      "code": "foreach ($order->products as $product) {\n $key = $product->product_key->where('status', '=', 0)->first();\n $key->status = '1';\n $key->reserve_order = $order->id;\n $key->save();\n}",
      "language": "php"
    }
  ]
}
[/block]
After we have placed the ordered goods on hold, we change the status of the order to “MINING” and expect the “PAID” status from the payment system.