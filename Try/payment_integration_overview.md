# 集成支付系统到网站

很棒的计划！集成一个支持常规支付（如信用卡、PayPal）和加密货币（如 BTC、ETH 等）支付的系统到你的网站，是提高用户体验和扩大受众的好方式。以下是实现这一目标的建议方案：

---

## 🧰 一、常用库和第三方支付服务

### 1. 常规支付（信用卡、PayPal 等）

#### ✅ 推荐服务：
- **Stripe**：支持信用卡、Apple Pay、Google Pay 等。
- **PayPal SDK**：适合已有 PayPal 用户的支付需求。
- **Square**：支持 POS 及在线支付。

#### 📦 常用库：
- `stripe` (Node.js SDK)
- `paypal-rest-sdk` 或 `@paypal/checkout-server-sdk`
- `axios` 用于调用任何 REST API

### 2. 加密货币支付

#### ✅ 推荐服务：
- **Coinbase Commerce**：接受 BTC、ETH、USDC 等。
- **NOWPayments**：支持多种币种与自动结算。
- **BitPay**：支持 BTC、ETH、DOGE 等常用币。
- **BTCPay Server**（自托管）：完全控制权。

#### 📦 常用库：
- `coinbase-commerce-node`
- `axios`（调用 Coinbase、NOWPayments 等 API）
- Webhook 处理：`express` + `body-parser`

---

## 🛠️ 二、具体实现步骤

### 1. 注册并获取 API Key
到各平台创建账户并获取 API Key 和 Webhook Secret（通常用于验证回调）。

### 2. 服务端配置（以 Node.js 为例）

#### 创建 Express 服务器
```bash
npm install express body-parser stripe coinbase-commerce-node
```

#### 示例代码（Stripe + Coinbase）：
```js
const express = require('express');
const bodyParser = require('body-parser');
const Stripe = require('stripe');
const coinbase = require('coinbase-commerce-node');

const stripe = Stripe('your-stripe-secret-key');
const { Client, resources } = coinbase;
const { Charge } = resources;

Client.init('your-coinbase-api-key');

const app = express();
app.use(bodyParser.json());

// Stripe 支付接口
app.post('/api/pay/stripe', async (req, res) => {
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: [{
      price_data: {
        currency: 'usd',
        product_data: {
          name: 'Test Product',
        },
        unit_amount: 2000,
      },
      quantity: 1,
    }],
    mode: 'payment',
    success_url: 'https://your-site.com/success',
    cancel_url: 'https://your-site.com/cancel',
  });

  res.json({ url: session.url });
});

// Coinbase 加密支付接口
app.post('/api/pay/crypto', async (req, res) => {
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
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## 🔔 三、Webhook 回调处理（支付结果通知）

你需要设置 Webhook 接口供 Stripe/Coinbase 等回调支付状态：

```js
app.post('/webhook/stripe', bodyParser.raw({ type: 'application/json' }), (req, res) => {
  const sig = req.headers['stripe-signature'];
  let event;

  try {
    event = stripe.webhooks.constructEvent(req.body, sig, 'your-stripe-webhook-secret');
  } catch (err) {
    return res.status(400).send(`Webhook Error: ${err.message}`);
  }

  if (event.type === 'checkout.session.completed') {
    const session = event.data.object;
    console.log('Payment success:', session);
    // 更新订单状态
  }

  res.json({ received: true });
});
```

---

## 🌍 四、前端触发支付

通过按钮调用后端 `/api/pay/stripe` 或 `/api/pay/crypto` 并跳转到返回的支付页面 URL。

---

## ✅ 五、安全建议

- 永远不要在前端暴露 API Secret Key  
- 验证所有 Webhook 签名  
- 使用 HTTPS（通过 Nginx 配置）  
- 记录支付状态并与订单系统关联

---

## 结语

如果你使用的是 Next.js 或其他框架，我也可以帮你整合进项目结构中。你对哪种支付平台更倾向？我可以给出更针对性的集成建议。
