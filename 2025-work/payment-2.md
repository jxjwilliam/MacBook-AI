# Integrating Payment Systems into Your Website

This document provides a comprehensive guide to integrating traditional payment methods (credit cards, PayPal) and cryptocurrency payments into your Node.js website. It covers setting up environment variables, installing dependencies, implementing server-side endpoints, handling webhooks, and front-end integration.

## Prerequisites

- Node.js v14 or later  
- An HTTPS-enabled server (e.g., configured via Nginx) for secure communications  
- Accounts and API credentials for:
  - Stripe
  - PayPal Developer
  - Coinbase Commerce
  - NOWPayments
  - BitPay

## Environment Variables

```bash
# Stripe
STRIPE_SECRET_KEY=sk_test_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx

# PayPal
PAYPAL_CLIENT_ID=your-paypal-client-id
PAYPAL_CLIENT_SECRET=your-paypal-client-secret

# Coinbase Commerce
COINBASE_API_KEY=your-coinbase-api-key
COINBASE_WEBHOOK_SECRET=your-coinbase-webhook-secret

# NOWPayments
NOWPAYMENTS_API_KEY=your-nowpayments-api-key

# BitPay
BITPAY_CONFIG_PATH=/path/to/BitPay.config.json
BITPAY_TOKEN=your-bitpay-token
```

## Installing Dependencies

```bash
npm install express body-parser axios dotenv
npm install stripe @paypal/checkout-server-sdk coinbase-commerce-node @nowpaymentsio/nowpayments-api-js bitpay-sdk
```

## Directory Structure

```
/backend
  index.js
  .env
  routes/
    payments.js
    webhooks.js
```

## Server Implementation

### Express App Setup

```js
require('dotenv').config();
const express = require('express');
const bodyParser = require('body-parser');

const paymentRoutes = require('./routes/payments');
const webhookRoutes = require('./routes/webhooks');

const app = express();
app.use(bodyParser.json());
app.use('/payments', paymentRoutes);
app.use('/webhooks', webhookRoutes);

app.listen(3000, () => console.log('Server running on port 3000'));
```

### Stripe Integration

Create a PaymentIntent on the server using Stripe's Node.js library, then confirm it on the client with Stripe.js. citeturn0search6turn0search15

```js
// routes/payments.js
const router = require('express').Router();
const Stripe = require('stripe');
const stripe = Stripe(process.env.STRIPE_SECRET_KEY);

router.post('/stripe', async (req, res) => {
  const { amount, currency } = req.body;
  const paymentIntent = await stripe.paymentIntents.create({
    amount,
    currency,
  });
  res.json({ clientSecret: paymentIntent.client_secret });
});
module.exports = router;
```

#### Webhook Handling

```js
// routes/webhooks.js
const router = require('express').Router();
const Stripe = require('stripe');
const stripe = Stripe(process.env.STRIPE_SECRET_KEY);
const endpointSecret = process.env.STRIPE_WEBHOOK_SECRET;
const bodyParser = require('body-parser');

router.post('/stripe', bodyParser.raw({ type: 'application/json' }), (req, res) => {
  const sig = req.headers['stripe-signature'];
  let event;
  try {
    event = stripe.webhooks.constructEvent(req.body, sig, endpointSecret);
  } catch (err) {
    return res.status(400).send(`Webhook Error: ${err.message}`);
  }
  if (event.type === 'payment_intent.succeeded') {
    // handle successful payment
  }
  res.json({ received: true });
});
module.exports = router;
```

### PayPal Integration

Use the PayPal Checkout Server SDK to create and capture orders. citeturn2search0turn2search2

```js
// routes/payments.js (continued)
const paypal = require('@paypal/checkout-server-sdk');
let environment = new paypal.core.SandboxEnvironment(
  process.env.PAYPAL_CLIENT_ID,
  process.env.PAYPAL_CLIENT_SECRET
);
let client = new paypal.core.PayPalHttpClient(environment);

router.post('/paypal', async (req, res) => {
  const request = new paypal.orders.OrdersCreateRequest();
  request.requestBody({
    intent: 'CAPTURE',
    purchase_units: [{ amount: { currency_code: 'USD', value: '100.00' } }],
  });
  const order = await client.execute(request);
  res.json({ id: order.result.id });
});

router.post('/paypal/capture', async (req, res) => {
  const { orderID } = req.body;
  const request = new paypal.orders.OrdersCaptureRequest(orderID);
  const capture = await client.execute(request);
  res.json(capture.result);
});
module.exports = router;
```

### Coinbase Commerce Integration

Initialize the client and create charges for crypto payments. citeturn0search4

```js
// routes/payments.js (continued)
const { Client, resources } = require('coinbase-commerce-node');
Client.init(process.env.COINBASE_API_KEY);
const { Charge } = resources;

router.post('/crypto/coinbase', async (req, res) => {
  const charge = await Charge.create({
    name: 'Product Name',
    description: 'Description',
    local_price: { amount: '10.00', currency: 'USD' },
    pricing_type: 'fixed_price',
    redirect_url: 'https://your-site.com/success',
    cancel_url: 'https://your-site.com/cancel',
  });
  res.json({ url: charge.hosted_url });
});
```

#### Webhook Handling

```js
// routes/webhooks.js (continued)
router.post('/crypto/coinbase/webhook', bodyParser.raw({ type: 'application/json' }), (req, res) => {
  const event = require('coinbase-commerce-node').Webhook.verifyEventBody(
    req.body,
    req.headers['x-cc-webhook-signature'],
    process.env.COINBASE_WEBHOOK_SECRET
  );
  if (event.type === 'charge:confirmed') {
    // handle confirmed charge
  }
  res.json({ received: true });
});
module.exports = router;
```

### NOWPayments Integration

Use the NOWPayments API client to accept a wide range of cryptocurrencies. citeturn0search2turn0search11

```js
// routes/payments.js (continued)
const NowPayments = require('@nowpaymentsio/nowpayments-api-js');
const nowpayments = new NowPayments(process.env.NOWPAYMENTS_API_KEY);

router.post('/crypto/nowpayments', async (req, res) => {
  const payment = await nowpayments.createPayment({
    price_amount: '10',
    price_currency: 'usd',
    pay_currency: 'btc',
    order_id: 'order123',
    order_description: 'Test Order',
  });
  res.json({ url: payment.payment_url });
});
module.exports = router;
```

### BitPay Integration

Install and configure the BitPay SDK, then create invoices. citeturn1search0turn1search8

```js
// routes/payments.js (continued)
const BitPay = require('bitpay-sdk');
const client = BitPay.Client.createClientByConfig(process.env.BITPAY_CONFIG_PATH);

router.post('/crypto/bitpay', async (req, res) => {
  const invoice = await client.post('invoices', {
    price: 10,
    currency: 'USD',
    token: process.env.BITPAY_TOKEN,
  });
  res.json({ url: invoice.data.url });
});
module.exports = router;
```

## Front-End Integration

- **Stripe.js**: Use `@stripe/stripe-js` to collect payment details and confirm PaymentIntents on the client.  
- **PayPal JS SDK**: Include `<script src="https://www.paypal.com/sdk/js?client-id=YOUR_CLIENT_ID"></script>` to render buttons. citeturn2search4  
- **Redirect to Hosted Pages**: For Coinbase Commerce, NOWPayments, and BitPay, redirect users to the hosted payment pages returned by the API.

## Security and Best Practices

- Store all API secrets in environment variables.  
- Validate and verify all webhook signatures.  
- Use HTTPS for all endpoints.  
- Implement idempotency keys for safe retries.  
- Log payment events for auditing.

## Testing and Go Live

- Use Stripe, PayPal, and Coinbase sandbox/test modes for testing.  
- Monitor webhooks and test all failure and success scenarios.  
- Switch to live API keys and endpoints before going live.
