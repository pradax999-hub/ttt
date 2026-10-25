# Transfer by Van

A private airport transfer and tour booking platform for Prague. The repository contains the customer-facing web app, the booking backend, Stripe payment handling, driver tracking, and the internal operator dashboard.

## Overview

Transfer by Van is designed for airport pickups, city transfers, private tours, and multi-stop route bookings. It supports real road-distance pricing, address search, online payment, live driver updates, and automated customer email confirmations.

The project is currently structured as a working web app with a local-first backend and deployment-ready configuration. Before production launch, make sure the required environment variables are configured and payment/webhook flows are verified in your target environment.

## Features

- Airport transfers from/to Prague Airport (PRG)
- One-way and round-trip pricing based on real route distance
- Multi-stop route support for custom itineraries and tours
- Geoapify-powered address search and hotel/place discovery
- Stripe checkout integration with order status checks
- Email confirmations and dispatch notifications
- Driver tracking endpoint for live position updates
- Operator dashboard for reservation search, order details, and CSV export
- Tour booking flow for private day trips from Prague

## Stack

- Frontend: static HTML/CSS/JS in `index.html`
- Backend: Node.js + Express in `server.js`
- Pricing logic: `pricing.js`
- Persistence: JSON file storage (`data/` and configurable `DATA_FILE`)
- Payments: Stripe
- Address lookup: Geoapify
- Email: Nodemailer / SMTP

## Project structure

```text
.
├── index.html           # customer-facing web app
├── server.js            # HTTP API, Stripe, auth, orders, emails
├── pricing.js           # fare calculation logic
├── pricing.test.js     # pricing tests
├── render.yaml         # Render deployment blueprint
├── package.json        # Node.js project definition
├── .env.example        # sample environment configuration
├── data/               # bookings and persisted order data
├── images/             # vehicle and fleet media
├── assets/             # static app assets / fonts
├── prmpt/              # additional front-end pages / promo content
└── README.md           # project documentation
```

## Quick start

Requirements:

- Node.js 22+
- npm

```bash
npm install
cp .env.example .env
npm start
```

Then open:

```text
http://localhost:3000
```

## Required configuration

Create a `.env` file based on `.env.example` and set the following values before running the app in a real environment:

```env
PORT=3000
CLIENT_URL=http://localhost:3000
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
GEOAPIFY_API_KEY=
DRIVER_ADMIN_TOKEN=
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=
EMAIL_PASS=
EMAIL_FROM=
DISPATCH_EMAIL=
PHONE=
WHATSAPP=
SUPPORT_EMAIL=
FLIGHT_PROVIDER=aviationstack
FLIGHT_API_KEY=
```

Important:

- Keep API keys and secrets in the server environment only.
- Do not expose secrets in client-side HTML or public source files.
- For production, configure `CLIENT_URL` with your exact HTTPS domain.
- `DRIVER_ADMIN_TOKEN` is required for protected operator endpoints.

## Deployment

### Render

This repository includes `render.yaml` and is ready to be deployed as a Node.js service on Render.

1. Connect the repository to Render.
2. Confirm the blueprint in `render.yaml`.
3. Set all required environment variables in the Render dashboard.
4. Register the Stripe webhook endpoint after deployment.

### VPS / self-hosted

```bash
npm install
npm start
```

For production, use a process manager such as PM2 or a managed platform and keep the `.env` file outside the repository.

## Payment and webhook flow

This app supports Stripe checkout and order-status checks. Webhook handling is configured through:

```text
https://YOUR-DOMAIN/api/webhook
```

The actual webhook secret should be set as `STRIPE_WEBHOOK_SECRET`. Even without a webhook, the backend can poll Stripe status for pending orders, but webhook registration is recommended for reliable confirmation.

## Operator access

Protected operator routes are guarded by `DRIVER_ADMIN_TOKEN` and are not designed for public access. Only the backend should validate the token, and it should never be stored in browser storage or the public URL.

## API highlights

```text
POST /api/checkout
GET /api/order-status?orderId=
GET /api/driver/:orderId
POST /api/driver/:orderId
GET /api/flight?fl=LH1400&date=
GET /api/geocode?q=
POST /api/quote
GET /api/config
POST /api/test-email
GET /webhook-setup
```

## Security and operational notes

- All external keys stay on the server.
- Cross-origin requests are restricted to configured origins.
- Operator responses are sent without public caching.
- Sensitive order data should only be stored in trusted storage.
- Use HTTPS in production and verify your webhook endpoint before accepting live payments.

## Status

This repository is a functional booking platform for local testing, staging, and controlled deployment. Production readiness depends on proper env setup, domain configuration, payment verification, and live webhook testing.

## License

This project is provided as-is for private or internal deployment. Review your hosting, legal, and payment-compliance requirements before operating it in production.
