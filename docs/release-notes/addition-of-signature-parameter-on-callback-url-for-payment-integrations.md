---
title: "Addition of signature parameter on callback URL for payment integrations"
slug: "addition-of-signature-parameter-on-callback-url-for-payment-integrations"
type: "improved"
createdAt: "2022-10-07T20:55:54.566Z"
hidden: false
excerpt: "The callback URL now has an additional `X-VTEX-signature` parameter. This parameter contains a signature token to identify that the request has been generated from VTEX as a security measure."
---

For some payment methods, the result of the payment cannot be obtained immediately after the request, returning an `undefined` status. These methods are classified as asynchronous payments. To deal with these methods at VTEX we use [callbacks](https://help.vtex.com/tutorial/payment-provider-protocol--RdsT2spdq80MMwwOeEq0m#payment-authorization), so the provider can tell our gateway later if the payment is approved or denied.

When making asynchronous payments through a [payment integration](https://developers.vtex.com/vtex-rest-api/docs/payments-integration-payment-provider-protocol), our gateway sends a URL in the `callbackUrl` field of the [Create Payment](https://developers.vtex.com/vtex-rest-api/reference/createpayment) request. This field contains the callback URL that the payment provider will have to call and deliver the final payment status to our gateway.

The callback URL now has an additional `X-VTEX-signature` parameter. This parameter contains a signature token to identify that the request has been generated from VTEX as a security measure. The signature token has at most 32 characters. You can check an example of callback URL with the signature token below:  

```
https://gatewayqa.vtexpayments.com.br/api/pvt/payment-provider/transactions/8FB0F111111122222333344449984ACB/payments/A2A9A25B11111111222222333327883C/callback?accountName=teampaymentsintegrations&X-VTEX-signature=R123456789aBcDeFGHij1234567890tk
```

In the [Transactions page of the Admin](https://help.vtex.com/en/tutorial/how-to-view-the-orders-details--tutorials_452), the signature token appears masked for security reasons, as in this example: `X-VTEX-signature=Rj******tk`.

When making the callback request, we recommend that payment providers use the callback URL exactly as received. **The use of the** `X-VTEX-signature` **parameter will be mandatory from April 30, 2023 for security reasons. Callback requests made without this parameter will not work after the deadline.**

For more information, see references below:
- [Create Payment endpoint](https://developers.vtex.com/vtex-rest-api/reference/createpayment#callback-url)
- [Payment Provider Protocol](https://help.vtex.com/en/tutorial/payment-provider-protocol--RdsT2spdq80MMwwOeEq0m#callback-url)
- [Purchase Flows](https://developers.vtex.com/docs/guides/payments-integration-purchase-flows#authorization)
