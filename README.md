# PayPal IPN Verification

A simple NodeJS package for verifying PayPal IPN messages.

## Installation
```sh
$ npm install paypal-ipn
```

## Usage
There is only one function, `verify`, which is used to verify any IPN messages you receive:

```javascript
ipn.verify(ipn_params, callback);
```

`ipn_params` is the dictionary of POST values sent to your IPN script by PayPal. Don't modify the dict in any way, just pass it directly to `ipn.verify` to check if the IPN message is valid.


Example code:

```javascript
// Must respond to PayPal IPN request with an empty 200 first, if using Express uncomment the following:
// res.send(200);

var ipn = require('paypal-ipn');

ipn.verify(params, function callback(err, msg) {
  if (err) {
    console.error(err);
  } else {
    // Do stuff with original params here

    if (params.payment_status == 'Completed') {
      // Payment has been confirmed as completed
    }
  }
});
```

Note that all the package does is confirm that the IPN message is valid. After this, you will still need to make some more checks:

* Confirm that the `payment_status` is `Completed`.

* Use the transaction ID to verify that the transaction has not already been processed, which prevents duplicate transactions from being processed.

* Validate that the receiver's email address is registered to you.

* Verify that the price, item description, and so on, match the transaction on your website.

You can find more information on the [PayPal documentation for IPN](https://cms.paypal.com/cgi-bin/marketingweb?cmd=_render-content&content_ID=developer/e_howto_admin_IPNIntro).

### The callback
The callback has two parameters, `err` and `msg`.

If `err` is null then the IPN is valid and you can continue processing the payment. `msg` is always `VERIFIED` then.

In case IPN was invalid or the http request failed `err` holds the Error object.

### ExpressJS
`paypal-ipn` works fine with [ExpressJS](http://expressjs.com/) or any other web framework.

All you need to do is pass in the request parameters to `ipn.verify`.

In ExpressJS, the request parameters are in `req.body`:

```javascript
ipn.verify(req.body, callback_function);
```
