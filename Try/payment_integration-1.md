# Payment System Integration Guide

This guide covers the integration of a payment system into your website that supports both traditional payment methods (such as credit cards and PayPal) and cryptocurrency payments. The implementation is based on using third-party API calls, and this document provides detailed steps, code samples, and security considerations.

---

## Table of Contents

1. [Overview](#overview)
2. [Supported Payment Methods and Libraries](#supported-payment-methods-and-libraries)
   - [Traditional Payments](#traditional-payments)
   - [Cryptocurrency Payments](#cryptocurrency-payments)
3. [Implementation Steps](#implementation-steps)
   - [Step 1: Account Setup and API Keys](#step-1-account-setup-and-api-keys)
   - [Step 2: Server-Side Configuration (Node.js Example)](#step-2-server-side-configuration-nodejs-example)
   - [Step 3: Payment Endpoint Implementation](#step-3-payment-endpoint-implementation)
   - [Step 4: Webhook Configuration and Handling](#step-4-webhook-configuration-and-handling)
   - [Step 5: Frontend Integration](#step-5-frontend-integration)
4. [Security Best Practices](#security-best-practices)
5. [Troubleshooting and Best Practices](#troubleshooting-and-best-practices)
6. [Conclusion](#conclusion)

---

## Overview

Integrating a payment system that covers both common payment methods and crypto payments can expand your user base and improve user experience. This guide focuses on integrating platforms such as **Stripe** for traditional payments and **Coinbase Commerce** for cryptocurrency payments. The sample code below uses Node.js and Express, but the concepts can be adapted to other languages or frameworks.

---

## Supported Payment Methods and Libraries

### Traditional Payments

#### Recommended Services:
- **Stripe**: Offers support for credit cards, Apple Pay, Google Pay, and more.
- **PayPal**: Widely used for online payments; consider using the official SDKs such as `paypal-rest-sdk` or `@paypal/checkout-server-sdk`.
- **Square**: Also supports point-of-sale (POS) and online payments.

#### Common Libraries:
- **Stripe Node.js SDK** (`stripe`)
- **PayPal SDK** (`paypal-rest-sdk` or `@paypal/checkout-server-sdk`)
- **HTTP Request Libraries** like `axios` for interacting with REST APIs

### Cryptocurrency Payments

#### Recommended Services:
- **Coinbase Commerce**: Supports multiple cryptocurrencies including BTC, ETH, and USDC.
- **NOWPayments**: Offers multi-currency support with automatic settlements.
- **BitPay**: Supports several cryptocurrencies including BTC, ETH, and DOGE.
- **BTCPay Server**: A self-hosted solution for complete control over the payment process.

#### Common Libraries:
- **Coinbase Commerce Node.js SDK** (`coinbase-commerce-node`)
- **HTTP Request Libraries** like `axios` for direct API calls
- **Express** (with `body-parser`) for handling webhooks and incoming payment notifications

---

## Implementation Steps

### Step 1: Account Setup and API Keys

1. **Register for Accounts**: Create accounts on the desired payment platforms (e.g., Stripe and Coinbase Commerce).
2. **Obtain API Keys**: Retrieve your API keys and webhook secrets from the dashboard of each service.
3. **Environment Variables**: Store API keys and secrets in environment variables to keep them secure.

### Step 2: Server-Side Configuration (Node.js Example)

Set up a basic Node.js server using Express. Install the necessary packages:

```bash
npm install express body-parser stripe coinbase-commerce-node
```

Create a file (e.g., server.js) and set up the server:

```js
const express = require('express');
const bodyParser = require('body-parser');
const Stripe = require('stripe');
const coinbase = require('coinbase-commerce-node');

// Initialize API Clients with your secret keys
const stripe = Stripe(process.env.STRIPE_SECRET_KEY);
const { Client, resources } = coinbase;
const { Charge } = resources;

Client.init(process.env.COINBASE_API_KEY);

const app = express();

// Use JSON parser for incoming requests
app.use(bodyParser.json());
```

Step 3: Payment Endpoint Implementation

Stripe Payment Endpoint
Create an endpoint to handle Stripe payments. This endpoint creates a checkout session and returns the URL for the client to complete the payment.

```js
app.post('/api/pay/stripe', async (req, res) => {
  try {
    const session = await stripe.checkout.sessions.create({
      payment_method_types: ['card'],
      line_items: [{
        price_data: {
          currency: 'usd',
          product_data: {
            name: 'Test Product',
          },
          unit_amount: 2000, // Amount in cents (i.e., $20.00)
        },
        quantity: 1,
      }],
      mode: 'payment',
      success_url: 'https://your-site.com/success?session_id={CHECKOUT_SESSION_ID}',
      cancel_url: 'https://your-site.com/cancel',
    });

    res.json({ url: session.url });
  } catch (error) {
    console.error('Stripe Checkout Error:', error);
    res.status(500).json({ error: 'Internal Server Error' });
  }
});
```

Coinbase Commerce (Crypto) Payment Endpoint

Similarly, create an endpoint for cryptocurrency payments using Coinbase Commerce:

```js
app.post('/api/pay/crypto', async (req, res) => {
  try {
    const chargeData = {
      name: 'Test Product',
      description: 'Pay with crypto',
      pricing_type: 'fixed_price',
      local_price: {
        amount: '20.00',
        currency: 'USD',
      },
      redirect_url: 'https://your-site.com/success',
      cancel_url: 'https://your-site.com/cancel',
    };

    const charge = await Charge.create(chargeData);
    res.json({ url: charge.hosted_url });
  } catch (error) {
    console.error('Coinbase Commerce Error:', error);
    res.status(500).json({ error: 'Internal Server Error' });
  }
});
```

Step 4: Webhook Configuration and Handling
Handling webhook callbacks is crucial to updating the order status after payment confirmation.

Stripe Webhook Example
```js
// Use raw body parser for Stripe webhook signature validation
app.post('/webhook/stripe', bodyParser.raw({ type: 'application/json' }), (req, res) => {
  const sig = req.headers['stripe-signature'];
  let event;

  try {
    event = stripe.webhooks.constructEvent(req.body, sig, process.env.STRIPE_WEBHOOK_SECRET);
  } catch (err) {
    console.error('Stripe Webhook Error:', err.message);
    return res.status(400).send(`Webhook Error: ${err.message}`);
  }

  // Handle the checkout session completed event
  if (event.type === 'checkout.session.completed') {
    const session = event.data.object;
    console.log('Stripe Payment Success:', session);
    // Update order status in your database
  }

  res.json({ received: true });
});
```