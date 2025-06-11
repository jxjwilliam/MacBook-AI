# Payment Integration Guide - Stripe, PayPal & Cryptocurrency

## Table of Contents
1. [Overview](#overview)
2. [Stripe Integration](#stripe-integration)
3. [PayPal Integration](#paypal-integration)
4. [Cryptocurrency Integration](#cryptocurrency-integration)
5. [Payment Security](#payment-security)
6. [Testing & Validation](#testing--validation)
7. [Best Practices](#best-practices)

## Overview

This guide covers implementing multiple payment providers in your NextJS application, providing customers with flexible payment options while maintaining security and compliance.

### Payment Provider Comparison

| Feature | Stripe | PayPal | Cryptocurrency |
|---------|--------|--------|----------------|
| Setup Complexity | Medium | Low | High |
| Transaction Fees | 2.9% + 30¢ | 2.9% + 30¢ | Network fees |
| Settlement Time | 2-7 days | 1-3 days | Minutes |
| Chargeback Protection | Yes | Yes | No |
| Global Reach | 46+ countries | 200+ countries | Worldwide |

## Stripe Integration

### Installation & Setup

```bash
npm install stripe @stripe/stripe-js @stripe/react-stripe-js
```

### Environment Configuration

```typescript
// .env.local
STRIPE_SECRET_KEY=sk_test_...
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...
```

### Stripe Client Configuration

```typescript
// lib/stripe.ts
import Stripe from 'stripe';
import { loadStripe } from '@stripe/stripe-js';

// Server-side Stripe client
export const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, {
  apiVersion: '2023-10-16',
  appInfo: {
    name: 'NextJS App',
    version: '1.0.0',
  },
});

// Client-side Stripe promise
export const stripePromise = loadStripe(
  process.env.NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY!
);
```

### Payment Intent API Route

```typescript
// app/api/create-payment-intent/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { stripe } from '@/lib/stripe';
import { auth } from '@/lib/auth';

export async function POST(request: NextRequest) {
  try {
    const session = await auth();
    if (!session?.user) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }

    const { amount, currency = 'usd', metadata = {} } = await request.json();

    const paymentIntent = await stripe.paymentIntents.create({
      amount: Math.round(amount * 100), // Convert to cents
      currency,
      automatic_payment_methods: {
        enabled: true,
      },
      metadata: {
        userId: session.user.id,
        ...metadata,
      },
    });

    return NextResponse.json({
      clientSecret: paymentIntent.client_secret,
      paymentIntentId: paymentIntent.id,
    });
  } catch (error) {
    console.error('Payment intent creation failed:', error);
    return NextResponse.json(
      { error: 'Failed to create payment intent' },
      { status: 500 }
    );
  }
}
```

### React Payment Component

```tsx
// components/payments/StripeCheckout.tsx
'use client';

import React, { useState } from 'react';
import {
  PaymentElement,
  Elements,
  useStripe,
  useElements,
} from '@stripe/react-stripe-js';
import { stripePromise } from '@/lib/stripe';

interface CheckoutFormProps {
  onSuccess: (paymentIntentId: string) => void;
  onError: (error: string) => void;
}

const CheckoutForm: React.FC<CheckoutFormProps> = ({ onSuccess, onError }) => {
  const stripe = useStripe();
  const elements = useElements();
  const [isLoading, setIsLoading] = useState(false);
  const [errorMessage, setErrorMessage] = useState<string | null>(null);

  const handleSubmit = async (event: React.FormEvent) => {
    event.preventDefault();

    if (!stripe || !elements) {
      return;
    }

    setIsLoading(true);
    setErrorMessage(null);

    try {
      // Trigger form validation and wallet collection
      const { error: submitError } = await elements.submit();
      if (submitError) {
        setErrorMessage(submitError.message);
        return;
      }

      // Create payment intent
      const response = await fetch('/api/create-payment-intent', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          amount: 29.99, // Your amount
          currency: 'usd',
          metadata: {
            productId: 'prod_123',
          },
        }),
      });

      const { clientSecret, paymentIntentId } = await response.json();

      // Confirm payment
      const { error } = await stripe.confirmPayment({
        elements,
        clientSecret,
        confirmParams: {
          return_url: `${window.location.origin}/payment/success`,
        },
        redirect: 'if_required',
      });

      if (error) {
        setErrorMessage(error.message);
        onError(error.message);
      } else {
        onSuccess(paymentIntentId);
      }
    } catch (error) {
      const message = error instanceof Error ? error.message : 'Payment failed';
      setErrorMessage(message);
      onError(message);
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      <PaymentElement
        options={{
          layout: 'accordion',
          defaultValues: {
            billingDetails: {
              name: 'Customer Name',
            },
          },
        }}
      />
      
      {errorMessage && (
        <div className="text-red-600 text-sm">{errorMessage}</div>
      )}
      
      <button
        type="submit"
        disabled={!stripe || !elements || isLoading}
        className="w-full bg-blue-600 text-white py-2 px-4 rounded-md hover:bg-blue-700 disabled:opacity-50"
      >
        {isLoading ? 'Processing...' : 'Pay Now'}
      </button>
    </form>
  );
};

interface StripeCheckoutProps {
  amount: number;
  onSuccess: (paymentIntentId: string) => void;
  onError: (error: string) => void;
}

export const StripeCheckout: React.FC<StripeCheckoutProps> = ({
  amount,
  onSuccess,
  onError,
}) => {
  const options = {
    mode: 'payment' as const,
    amount: Math.round(amount * 100),
    currency: 'usd',
    appearance: {
      theme: 'stripe' as const,
      variables: {
        colorPrimary: '#2563eb',
      },
    },
  };

  return (
    <Elements stripe={stripePromise} options={options}>
      <CheckoutForm onSuccess={onSuccess} onError={onError} />
    </Elements>
  );
};
```

### Webhook Handler

```typescript
// app/api/webhooks/stripe/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { stripe } from '@/lib/stripe';
import { supabase } from '@/lib/supabase';

export async function POST(request: NextRequest) {
  const body = await request.text();
  const signature = request.headers.get('stripe-signature');

  if (!signature) {
    return NextResponse.json({ error: 'No signature' }, { status: 400 });
  }

  try {
    const event = stripe.webhooks.constructEvent(
      body,
      signature,
      process.env.STRIPE_WEBHOOK_SECRET!
    );

    switch (event.type) {
      case 'payment_intent.succeeded':
        await handlePaymentSuccess(event.data.object);
        break;
      
      case 'payment_intent.payment_failed':
        await handlePaymentFailure(event.data.object);
        break;
      
      default:
        console.log(`Unhandled event type: ${event.type}`);
    }

    return NextResponse.json({ received: true });
  } catch (error) {
    console.error('Webhook error:', error);
    return NextResponse.json(
      { error: 'Webhook handler failed' },
      { status: 400 }
    );
  }
}

async function handlePaymentSuccess(paymentIntent: any) {
  const { metadata } = paymentIntent;
  
  // Update order status in database
  await supabase
    .from('orders')
    .update({
      status: 'paid',
      payment_intent_id: paymentIntent.id,
      paid_at: new Date().toISOString(),
    })
    .eq('user_id', metadata.userId);
}

async function handlePaymentFailure(paymentIntent: any) {
  const { metadata } = paymentIntent;
  
  // Log payment failure
  await supabase
    .from('payment_logs')
    .insert({
      user_id: metadata.userId,
      payment_intent_id: paymentIntent.id,
      status: 'failed',
      error_message: paymentIntent.last_payment_error?.message,
    });
}
```

### Subscription Management

```typescript
// lib/subscription.ts
import { stripe } from './stripe';

export async function createSubscription(
  customerId: string,
  priceId: string,
  paymentMethodId: string
) {
  try {
    // Attach payment method to customer
    await stripe.paymentMethods.attach(paymentMethodId, {
      customer: customerId,
    });

    // Set as default payment method
    await stripe.customers.update(customerId, {
      invoice_settings: {
        default_payment_method: paymentMethodId,
      },
    });

    // Create subscription
    const subscription = await stripe.subscriptions.create({
      customer: customerId,
      items: [{ price: priceId }],
      payment_settings: {
        payment_method_options: {
          card: {
            request_three_d_secure: 'if_required',
          },
        },
        payment_method_types: ['card'],
        save_default_payment_method: 'on_subscription',
      },
      expand: ['latest_invoice.payment_intent'],
    });

    return subscription;
  } catch (error) {
    console.error('Subscription creation failed:', error);
    throw error;
  }
}
```

## PayPal Integration

### Installation & Setup

```bash
npm install @paypal/react-paypal-js
```

### Environment Configuration

```typescript
// .env.local
PAYPAL_CLIENT_ID=your_paypal_client_id
PAYPAL_CLIENT_SECRET=your_paypal_client_secret
PAYPAL_MODE=sandbox # or production
```

### PayPal Component

```tsx
// components/payments/PayPalCheckout.tsx
'use client';

import React from 'react';
import {
  PayPalScriptProvider,
  PayPalButtons,
  usePayPalScriptReducer,
} from '@paypal/react-paypal-js';

interface PayPalCheckoutProps {
  amount: string;
  onSuccess: (details: any) => void;
  onError: (error: any) => void;
}

const PayPalButtonWrapper: React.FC<PayPalCheckoutProps> = ({
  amount,
  onSuccess,
  onError,
}) => {
  const [{ options, isPending }, dispatch] = usePayPalScriptReducer();

  return (
    <>
      {isPending && <div>Loading PayPal...</div>}
      <PayPalButtons
        style={{
          layout: 'vertical',
          color: 'gold',
          shape: 'rect',
          label: 'paypal',
        }}
        createOrder={async (data, actions) => {
          return actions.order.create({
            purchase_units: [
              {
                amount: {
                  value: amount,
                  currency_code: 'USD',
                },
              },
            ],
          });
        }}
        onApprove={async (data, actions) => {
          try {
            const details = await actions.order?.capture();
            onSuccess(details);
          } catch (error) {
            onError(error);
          }
        }}
        onError={(error) => {
          onError(error);
        }}
      />
    </>
  );
};

export const PayPalCheckout: React.FC<PayPalCheckoutProps> = (props) => {
  const initialOptions = {
    'client-id': process.env.NEXT_PUBLIC_PAYPAL_CLIENT_ID!,
    currency: 'USD',
    intent: 'capture',
  };

  return (
    <PayPalScriptProvider options={initialOptions}>
      <PayPalButtonWrapper {...props} />
    </PayPalScriptProvider>
  );
};
```

### PayPal Webhook Handler

```typescript
// app/api/webhooks/paypal/route.ts
import { NextRequest, NextResponse } from 'next/server';
import crypto from 'crypto';

export async function POST(request: NextRequest) {
  try {
    const body = await request.text();
    const headers = request.headers;
    
    // Verify PayPal webhook signature
    const isValid = verifyPayPalWebhook(body, headers);
    if (!isValid) {
      return NextResponse.json({ error: 'Invalid signature' }, { status: 401 });
    }

    const event = JSON.parse(body);

    switch (event.event_type) {
      case 'PAYMENT.CAPTURE.COMPLETED':
        await handlePayPalPaymentCompleted(event.resource);
        break;
      
      case 'PAYMENT.CAPTURE.DENIED':
        await handlePayPalPaymentDenied(event.resource);
        break;
      
      default:
        console.log(`Unhandled PayPal event: ${event.event_type}`);
    }

    return NextResponse.json({ status: 'success' });
  } catch (error) {
    console.error('PayPal webhook error:', error);
    return NextResponse.json({ error: 'Webhook failed' }, { status: 500 });
  }
}

function verifyPayPalWebhook(body: string, headers: Headers): boolean {
  // Implement PayPal webhook signature verification
  // This is a simplified version - use PayPal's SDK for production
  const signature = headers.get('paypal-transmission-sig');
  const certId = headers.get('paypal-cert-id');
  const timestamp = headers.get('paypal-transmission-time');
  
  // Verify signature using PayPal's public key
  return true; // Simplified for example
}
```

## Cryptocurrency Integration

### Bitcoin/Ethereum Integration

```typescript
// lib/crypto-payment.ts
import { ethers } from 'ethers';

interface CryptoPaymentRequest {
  amount: number;
  currency: 'BTC' | 'ETH';
  walletAddress: string;
  orderReference: string;
}

export class CryptoPaymentService {
  private btcApiUrl = 'https://api.blockcypher.com/v1/btc/main';
  private ethProvider = new ethers.JsonRpcProvider(process.env.ETH_RPC_URL);

  async createBitcoinPayment(request: CryptoPaymentRequest) {
    try {
      // Generate a unique Bitcoin address for this payment
      const response = await fetch(`${this.btcApiUrl}/addrs`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
      });

      const { address, private, public: publicKey } = await response.json();

      // Store payment request in database
      await this.storePaymentRequest({
        ...request,
        address,
        privateKey: private,
        publicKey,
      });

      return {
        address,
        amount: request.amount,
        qrCode: this.generateQRCode(address, request.amount),
      };
    } catch (error) {
      console.error('Bitcoin payment creation failed:', error);
      throw error;
    }
  }

  async createEthereumPayment(request: CryptoPaymentRequest) {
    try {
      // Generate new Ethereum wallet
      const wallet = ethers.Wallet.createRandom();
      
      // Store payment request
      await this.storePaymentRequest({
        ...request,
        address: wallet.address,
        privateKey: wallet.privateKey,
      });

      return {
        address: wallet.address,
        amount: request.amount,
        qrCode: this.generateQRCode(wallet.address, request.amount),
      };
    } catch (error) {
      console.error('Ethereum payment creation failed:', error);
      throw error;
    }
  }

  async monitorPayment(address: string, expectedAmount: number) {
    // Implement payment monitoring logic
    // This would typically involve setting up webhooks or polling
    return new Promise((resolve, reject) => {
      // Monitor blockchain for payments to the address
      const interval = setInterval(async () => {
        try {
          const balance = await this.getAddressBalance(address);
          if (balance >= expectedAmount) {
            clearInterval(interval);
            resolve(balance);
          }
        } catch (error) {
          clearInterval(interval);
          reject(error);
        }
      }, 30000); // Check every 30 seconds
    });
  }

  private async getAddressBalance(address: string): Promise<number> {
    // Implement balance checking for Bitcoin/Ethereum
    return 0;
  }

  private generateQRCode(address: string, amount: number): string {
    // Generate QR code for payment
    return `bitcoin:${address}?amount=${amount}`;
  }

  private async storePaymentRequest(data: any) {
    // Store in Supabase or your database
    const { supabase } = await import('@/lib/supabase');
    
    return await supabase
      .from('crypto_payments')
      .insert({
        address: data.address,
        amount: data.amount,
        currency: data.currency,
        order_reference: data.orderReference,
        status: 'pending',
        created_at: new Date().toISOString(),
      });
  }
}
```

### Crypto Payment Component

```tsx
// components/payments/CryptoCheckout.tsx
'use client';

import React, { useState, useEffect } from 'react';
import QRCode from 'qrcode.react';
import { CryptoPaymentService } from '@/lib/crypto-payment';

interface CryptoCheckoutProps {
  amount: number;
  currency: 'BTC' | 'ETH';
  onSuccess: (txHash: string) => void;
  onError: (error: string) => void;
}

export const CryptoCheckout: React.FC<CryptoCheckoutProps> = ({
  amount,
  currency,
  onSuccess,
  onError,
}) => {
  const [paymentAddress, setPaymentAddress] = useState<string>('');
  const [qrCodeData, setQrCodeData] = useState<string>('');
  const [isMonitoring, setIsMonitoring] = useState(false);
  const [timeRemaining, setTimeRemaining] = useState(900); // 15 minutes

  const cryptoService = new CryptoPaymentService();

  useEffect(() => {
    initializePayment();
  }, []);

  useEffect(() => {
    if (timeRemaining > 0) {
      const timer = setTimeout(() => {
        setTimeRemaining(timeRemaining - 1);
      }, 1000);
      return () => clearTimeout(timer);
    } else {
      onError('Payment timeout');
    }
  }, [timeRemaining]);

  const initializePayment = async () => {
    try {
      const payment = currency === 'BTC' 
        ? await cryptoService.createBitcoinPayment({
            amount,
            currency,
            walletAddress: paymentAddress,
            orderReference: `order_${Date.now()}`,
          })
        : await cryptoService.createEthereumPayment({
            amount,
            currency,
            walletAddress: paymentAddress,
            orderReference: `order_${Date.now()}`,
          });

      setPaymentAddress(payment.address);
      setQrCodeData(payment.qrCode);
      startMonitoring(payment.address);
    } catch (error) {
      onError('Failed to create crypto payment');
    }
  };

  const startMonitoring = async (address: string) => {
    setIsMonitoring(true);
    try {
      await cryptoService.monitorPayment(address, amount);
      onSuccess('payment_confirmed');
    } catch (error) {
      onError('Payment monitoring failed');
    } finally {
      setIsMonitoring(false);
    }
  };

  const formatTime = (seconds: number) => {
    const minutes = Math.floor(seconds / 60);
    const remainingSeconds = seconds % 60;
    return `${minutes}:${remainingSeconds.toString().padStart(2, '0')}`;
  };

  return (
    <div className="space-y-6">
      <div className="text-center">
        <h3 className="text-lg font-semibold">
          Pay with {currency}
        </h3>
        <p className="text-gray-600">
          Send exactly {amount} {currency} to the address below
        </p>
      </div>

      {paymentAddress && (
        <div className="text-center space-y-4">
          <QRCode
            value={qrCodeData}
            size={256}
            className="mx-auto"
          />
          
          <div className="bg-gray-100 p-4 rounded-lg">
            <p className="font-mono text-sm break-all">
              {paymentAddress}
            </p>
            <button
              onClick={() => navigator.clipboard.writeText(paymentAddress)}
              className="mt-2 text-blue-600 hover:underline"
            >
              Copy Address
            </button>
          </div>
        </div>
      )}

      <div className="text-center space-y-2">
        <p className="text-sm text-gray-600">
          Time remaining: {formatTime(timeRemaining)}
        </p>
        
        {isMonitoring && (
          <div className="flex items-center justify-center space-x-2">
            <div className="animate-spin rounded-full h-4 w-4 border-b-2 border-blue-600"></div>
            <span className="text-sm">Monitoring for payment...</span>
          </div>
        )}
      </div>

      <div className="text-xs text-gray-500 space-y-1">
        <p>• Send the exact amount to avoid delays</p>
        <p>• Payments typically confirm within 10-30 minutes</p>
        <p>• Do not send from an exchange wallet</p>
      </div>
    </div>
  );
};
```

## Payment Security

### PCI Compliance

```typescript
// lib/security.ts
export const securityHeaders = {
  'Strict-Transport-Security': 'max-age=31536000; includeSubDomains',
  'X-Content-Type-Options': 'nosniff',
  'X-Frame-Options': 'DENY',
  'X-XSS-Protection': '1; mode=block',
  'Referrer-Policy': 'strict-origin-when-cross-origin',
};

export function validatePaymentData(data: any): boolean {
  // Validate payment amount
  if (!data.amount || data.amount <= 0) {
    return false;
  }

  // Validate currency
  const allowedCurrencies = ['USD', 'EUR', 'GBP'];
  if (!allowedCurrencies.includes(data.currency)) {
    return false;
  }

  // Additional validation rules
  return true;
}

export function sanitizePaymentData(data: any) {
  return {
    amount: parseFloat(data.amount),
    currency: data.currency.toUpperCase(),
    metadata: Object.keys(data.metadata || {}).reduce((acc, key) => {
      acc[key] = String(data.metadata[key]).slice(0, 500);
      return acc;
    }, {} as Record<string, string>),
  };
}
```

### Rate Limiting

```typescript
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

const rateLimit = new Map<string, { count: number; resetTime: number }>();

export function middleware(request: NextRequest) {
  // Apply rate limiting to payment endpoints
  if (request.nextUrl.pathname.startsWith('/api/create-payment-intent')) {
    const ip = request.ip || 'unknown';
    const now = Date.now();
    const windowMs = 60 * 1000; // 1 minute
    const maxRequests = 10;

    if (!rateLimit.has(ip)) {
      rateLimit.set(ip, { count: 1, resetTime: now + windowMs });
    } else {
      const clientData = rateLimit.get(ip)!;
      
      if (now > clientData.resetTime) {
        clientData.count = 1;
        clientData.resetTime = now + windowMs;
      } else {
        clientData.count++;
        
        if (clientData.count > maxRequests) {
          return NextResponse.json(
            { error: 'Too many requests' },
            { status: 429 }
          );
        }
      }
    }
  }

  return NextResponse.next();
}
```

## Testing & Validation

### Stripe Test Cards

```typescript
// lib/test-data.ts
export const stripeTestCards = {
  visa: '4242424242424242',
  visaDebit: '4000056655665556',
  mastercard: '5555555555554444',
  amex: '378282246310005',
  declined: '4000000000000002',
  insufficientFunds: '4000000000009995',
  requiresAuthentication: '4000002500003155',
};

export const testPaymentScenarios = [
  {
    name: 'Successful Payment',
    card: stripeTestCards.visa,
    expectedResult: 'success',
  },
  {
    name: 'Declined Payment',
    card: stripeTestCards.declined,
    expectedResult: 'declined',
  },
  {
    name: '3D Secure Authentication',
    card: stripeTestCards.requiresAuthentication,
    expectedResult: 'requires_action',
  },
];
```

### Payment Testing Component

```tsx
// components/testing/PaymentTester.tsx
'use client';

import React, { useState } from 'react';
import { StripeCheckout } from '@/components/payments/StripeCheckout';
import { PayPalCheckout } from '@/components/payments/PayPalCheckout';
import { CryptoCheckout } from '@/components/payments/CryptoCheckout';

export const PaymentTester: React.FC = () => {
  const [selectedProvider, setSelectedProvider] = useState<'stripe' | 'paypal' | 'crypto'>('stripe');
  const [testAmount, setTestAmount] = useState(29.99);

  const handlePaymentSuccess = (result: any) => {
    console.log('Payment successful:', result);
    alert('Payment successful!');
  };

  const handlePaymentError = (error: string) => {
    console.error('Payment failed:', error);
    alert(`Payment failed: ${error}`);
  };

  return (
    <div className="max-w-md mx-auto p-6 bg-white rounded-lg shadow-lg">
      <h2 className="text-2xl font-bold mb-6">Payment Testing</h2>
      
      <div className="mb-4">
        <label className="block text-sm font-medium mb-2">
          Test Amount ($)
        </label>
        <input
          type="number"
          value={testAmount}
          onChange={(e) => setTestAmount(parseFloat(e.target.value))}
          className="w-full p-2 border rounded-md"
          step="0.01"
          min="0.01"
        />
      </div>

      <div className="mb-6">
        <label className="block text-sm font-medium mb-2">
          Payment Provider
        </label>
        <select
          value={selectedProvider}
          onChange={(e) => setSelectedProvider(e.target.value as any)}
          className="w-full p-2 border rounded-md"
        >
          <option value="stripe">Stripe</option>
          <option value="paypal">PayPal</option>
          <option value="crypto">Cryptocurrency</option>
        </select>
      </div>

      {selectedProvider === 'stripe' && (
        <StripeCheckout
          amount={testAmount}
          onSuccess={handlePaymentSuccess}
          onError={handlePaymentError}
        />
      )}

      {selectedProvider === 'paypal' && (
        <PayPalCheckout
          amount={testAmount.toString()}
          onSuccess={handlePaymentSuccess}
          onError={handlePaymentError}
        />
      )}

      {selectedProvider === 'crypto' && (
        <CryptoCheckout
          amount={testAmount}
          currency="BTC"
          onSuccess={handlePaymentSuccess}
          onError={handlePaymentError}
        />
      )}
    </div>
  );
};
```

## Best Practices

### 1. Security First
- Never store payment data on your servers
- Use HTTPS for all payment-related endpoints
- Implement proper webhook validation
- Follow PCI DSS compliance guidelines

### 2. User Experience
- Provide clear payment status feedback
- Support multiple payment methods
- Implement proper error handling
- Use loading states during processing

### 3. Error Handling
- Log all payment errors for debugging
- Provide user-friendly error messages
- Implement retry mechanisms for failed payments
- Monitor payment success rates

### 4. Testing Strategy
- Test with various payment scenarios
- Use provider test environments
- Implement automated payment testing
- Monitor payment flows in production

### 5. Performance Optimization
- Lazy load payment components
- Cache payment provider scripts
- Minimize payment form fields
- Optimize for mobile devices

This comprehensive payment integration guide provides you with the foundation to implement secure, reliable payment processing in your NextJS application using multiple providers.
