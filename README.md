````md
# 💳 🚀 CI/CD Pipeline to Azure VM with Docker (GitLab CI/CD)
      This project uses GitLab CI/CD to automate deployment to an Azure Virtual Machine using Docker over SSH.

**FlexiPay** is a scalable and secure custom payment gateway built with **Node.js**, **Express**, and **MongoDB**. It acts as a central platform to process payments through various providers including **Stripe**, **PayPal**, and digital wallets. Designed with extensibility in mind, FlexiPay can be embedded in SaaS platforms or marketplaces using a platform-owned strategy, managing merchant payments and weekly balance distributions.


## 📁 .gitlab-ci.yml Overview
   The pipeline includes the following stages:
- build (optional): Build Docker image
- deploy: SSH into Azure VM and deploy Docker container

Sample .gitlab-ci.yml

```bash
   stages:
     - deploy
   
   deploy:
     stage: deploy
     image: docker:latest
     services:
       - docker:dind
     variables:
       DOCKER_HOST: tcp://docker:2375/
       DOCKER_TLS_CERTDIR: ""
     before_script:
       - apk add --no-cache openssh
       - echo "$SSH_PRIVATE_KEY" > id_rsa
       - chmod 600 id_rsa
     script:
       - ssh -o StrictHostKeyChecking=no -i id_rsa $SSH_USER@$SSH_HOST "
           docker pull $DOCKER_IMAGE &&
           docker stop $CONTAINER_NAME || true &&
           docker rm $CONTAINER_NAME || true &&
           docker run -d --name $CONTAINER_NAME $DOCKER_IMAGE
         "
```
## 🔐 Environment Variables (GitLab → Settings → CI/CD → Variables)
   - SSH_PRIVATE_KEY
   - SSH_USER
   - SSH_HOST
   - DOCKER_IMAGE
   - CONTAINER_NAME


```bash
flexipay/
│
├── models/
│ ├── User.model.js
│ ├── PaymentMethod.model.js
│ └── Transaction.model.js
│
├── routes/
│ ├── payment.routes.js
│ ├── merchant.routes.js
│ └── webhook.routes.js
│
├── services/
│ ├── stripe.service.js
│ ├── paypal.service.js
│ └── scheduler.service.js
│
├── utils/
│ ├── paginate.js
│ ├── slice.js
│ └── response.js
│
├── jobs/
│ └── weeklyPayout.js
│
├── config/
│ └── stripe.js
│ └── paypal.js
│
└── app.js
```

## ⚙️ Setup & Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/flexipay.git
   cd flexipay
   ```
````

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Configure environment**

   - Create `.env` file based on `.env.example`:

     ```
     STRIPE_SECRET_KEY=sk_test_***
     PAYPAL_CLIENT_ID=***
     PAYPAL_SECRET=***
     MONGODB_URI=mongodb://localhost:27017/flexipay
     PORT=3000
     ```

4. **Run the app**

   ```bash
   npm run dev
   ```

---

## 🔐 Authentication

FlexiPay assumes you already have an auth system. It uses `req.user` to identify the current user (merchant or customer). You can plug in your own middleware or integrate with Passport/JWT.

---

## 🏗️ API Endpoints

### `POST /api/payments/create`

Create a new payment using selected payment method.

### `GET /api/payment-methods`

List current user's payment methods with optional filters:

```http
GET /api/payment-methods?filter[type]=stripe&filter[is_primary]=true
```

### `POST /api/payment-methods`

Add a new payment method (e.g., Stripe Customer ID, PayPal email, etc.)

### `POST /webhook/stripe`

Handles Stripe webhook events (`payment_succeeded`, `payment_failed`, etc.)

---

## ⏱️ Scheduled Payouts

A cron job runs weekly to distribute merchant balances based on accumulated transactions. This uses the `Transaction` model and aggregates balances per merchant, then initiates payouts via the payment provider API.

You can trigger it manually for testing:

```js
npm run payout:weekly
```

---

## 💡 How It Works (Stripe Example)

1. Merchant registers and adds Stripe as a payment method.
2. FlexiPay creates a Stripe customer for the user.
3. On checkout, a Stripe PaymentIntent is created.
4. Stripe handles card input + authentication.
5. On success, Stripe triggers the webhook.
6. FlexiPay updates the `Transaction` and records success.
7. Weekly, merchant balances are distributed via Stripe payouts.

---

## 🛡️ Best Practices Followed

- Mongoose schemas with field validation
- `.env` for all secrets and keys
- Separation of concerns (services, models, routes, jobs)
- Pagination utility with `limit`, `page`, `total`, `from`, `to`
- Map-style credentials storage (e.g., `{ customerId: "cus_abc" }`)
- Modular structure for adding future payment providers

---

## 📌 TODO / Future Features

- [ ] Add Apple Pay / Google Pay support
- [ ] Admin dashboard with analytics
- [ ] Real-time transaction tracking with WebSockets
- [ ] Merchant onboarding workflow (KYC, etc.)
- [ ] Invoicing and refund handling

---

## 👥 Contributing

Pull requests are welcome! For major changes, open an issue first to discuss what you’d like to change or enhance.

---

## 📝 License

MIT © 2025 FlexiPay

```

Would you like me to generate an example `.env`, a Postman collection, or include Stripe setup notes in the README?
```
