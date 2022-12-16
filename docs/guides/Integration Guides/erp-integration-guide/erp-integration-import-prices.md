---
title: "Import prices"
slug: "erp-integration-import-prices"
hidden: false
createdAt: "2020-03-11T20:58:11.051Z"
updatedAt: "2022-02-03T13:51:58.560Z"
---
In this step, you will send the current pricing information for all your products to VTEX.

## Before you start

It is common for store owners to delegate price calculations to the ERP. In such scenarios, the ERP is responsible for calculating the price of each SKU and sending this price to VTEX. 

As mentioned in the [Import products](https://developers.vtex.com/docs/erp-integration-import-products) step, Trade Policies are used in VTEX to differentiate the catalog, prices and logistics settings for each sales channel connected to your store. You may use them, for example, if you want the same list of SKUs to have different prices in each marketplace where your store is selling.

It’s also important to understand our [Pricing system architecture](https://help.vtex.com/tracks/prices-101--6f8pwCns3PJHqMvQSugNfP/7GptzvlPDVM11ojEjywIQx), based on three basic concepts:

  - **Prices**: the amount of money for which SKUs are listed for sale
  - **Price Tables**: a container that stores prices that should be applied in a given context
  - **Price Table Context**: conditions for application of a price table, such as a trade policy

Also, given a specific Price Table is selected according to the context, there are some definitions that should be known to understand the [computed price](https://help.vtex.com/tracks/prices-101--6f8pwCns3PJHqMvQSugNfP/7GptzvlPDVM11ojEjywIQx#computed-price):

  - **List price**: value displayed as the suggested retail price proposed by the supplier
  - **Base price**: reference value for the computed price of an SKU in all contexts
  - **Computed price**: retail price after applying the [price rules](https://help.vtex.com/tracks/prices-101--6f8pwCns3PJHqMvQSugNfP/2rBirbpB7wLnei4dQ9KGMW) of a specific context
  - **Fixed price**: fixed value that overrides the computed price for an SKU in a price table



[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/931b88e-image2.png",
        "image2.png",
        703,
        115,
        "#eceaed"
      ],
      "caption": "*The computed Price is obtained from the application of price rules on a base price*"
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/6458670-image6.png",
        "image6.png",
        703,
        115,
        "#f0eff1"
      ],
      "caption": "*A fixed price overrides the computed price for an SKU in a price table*"
    }
  ]
}
[/block]
So if you decide not to use price rules to compute your prices in VTEX, the way to do it is to send a single **base price** for each SKU sent by your ERP and set multiple **fixed prices** as needed to differentiate the value charged for each trade policy. If you would like a conceptual overview of our Pricing module, check out the [beginner track](https://help.vtex.com/tracks/prices-101--6f8pwCns3PJHqMvQSugNfP) in our Help Center.

## Set base price

To set the base price for an SKU, you should use the [Create/Edit Price](https://developers.vtex.com/vtex-rest-api/reference/getprice) endpoint in the Pricing API.

You should set a base price for each SKU. In between steps, you can use the [Get Price](https://developers.vtex.com/vtex-rest-api/reference/getprice) endpoint or visit the *Products > Prices > Price list* section of your Admin panel to check on your progress.

[block:callout]
{
  "type": "info",
  "body": "In the same [Create/Edit Price](https://developers.vtex.com/vtex-rest-api/reference/getprice) request, you may optionally set a list price. Additionally, you may set either a cost price or a markup value. By defining either one of them, the other will be calculated to conform to the formula `basePrice = costPrice * (1 + markup)`."
}
[/block]
## Set fixed prices for specific contexts

To set a fixed price for a specific context, you should use the [Create / Edit Fixed Prices](https://developers.vtex.com/vtex-rest-api/reference/getprice) endpoint in the Pricing API. This context is represented by either a Trade Policy or a custom Price Table, which can be associated to a campaign or a customer cluster.

You should set a fixed price for each price table / SKU combination that needs to be priced differently from the base price. In between steps, you can use the [Get Fixed Prices](https://developers.vtex.com/vtex-rest-api/reference/getprice) endpoint or visit the *Products > Prices > Price list* section of your Admin panel to check on your progress.

[block:callout]
{
  "type": "warning",
  "body": "The ```tradePolicyId``` parameter should be filled in with the Price Table name if you want to set fixed prices for a custom Price Table that is not associated with a specific Trade Policy."
}
[/block]

[block:callout]
{
  "type": "info",
  "body": "Keep in mind that [price rules](https://help.vtex.com/tracks/prices-101--6f8pwCns3PJHqMvQSugNfP/2rBirbpB7wLnei4dQ9KGMW) might be a better option than fixed prices if you are consistently applying the same criteria for SKUs in the same category, brand or markup range."
}
[/block]
## Wrapping up

If you’ve done things correctly, you should have imported all your prices, including the base prices for your products and the fixed prices that should be applied in specific contexts.