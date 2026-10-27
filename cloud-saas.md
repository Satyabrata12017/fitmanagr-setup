# SaaS / Cloud setup (optional)

[← Setup guide home](README.md)

You only need this when **you** run FitManagr as a multi-gym SaaS, with self-signup, free trials, monthly subscription billing and push notifications. A single customer's own installation doesn't need it.

## What the Cloud part does

| Function | Job |
|---|---|
| `createTenant` | Self-signup: creates the gym, owner login and trial on the backend |
| `startSubscription` / `razorpayWebhook` | Razorpay subscription billing; updates the gym's licence when it is charged, halted or cancelled |
| `syncLicences` | Nightly copy of licences and platform statistics to Firestore |
| `sendSms` | SMS reminders and OTPs through MSG91 (DLT templates) |
| `saasPaymentLink` | Razorpay payment link for a gym's subscription invoice |
| Firebase Auth and FCM | Email/Google login and push notifications |

## Steps

1. Create a **Firebase project** on the Blaze plan. Turn on Auth (Email/Password and Google), Firestore and Hosting.
2. Clone the private repos side by side:
   ```bash
   git clone https://<token>@github.com/Satyabrata12017/fitmanagr-cloud.git
   git clone https://<token>@github.com/Satyabrata12017/fitmanagr-frontend.git
   ```
3. Store the secrets. The values come from the backend's **Gym SaaS Settings** and your Razorpay account.
   ```bash
   cd fitmanagr-cloud
   npx firebase-tools functions:secrets:set FRAPPE_SECRET
   npx firebase-tools functions:secrets:set RAZORPAY_KEY_ID
   npx firebase-tools functions:secrets:set RAZORPAY_KEY_SECRET
   npx firebase-tools functions:secrets:set RAZORPAY_WEBHOOK_SECRET
   npx firebase-tools functions:secrets:set MSG91_AUTH_KEY      # only for SMS
   ```
4. On the backend server, install `firebase-admin` (Frappe v15/v16). Then put the service-account JSON path in **Gym SaaS Settings → Firebase Credentials Path**.
5. Deploy the functions, the Firestore rules and the web app:
   ```bash
   FIREBASE_PROJECT=<project-id> FRAPPE_URL=https://api.example.com \
   RAZORPAY_PLANS='{"Pro:monthly":"plan_xxx"}' ./scripts/deploy.sh
   ```
6. In Razorpay, point the webhook at the `razorpayWebhook` URL (events: `subscription.*`, `payment_link.paid`).
7. In the frontend `.env`, set `VITE_AUTH_MODE=firebase` and the `VITE_FIREBASE_*` values, then rebuild.

## Plans and trials

Prices, member limits and modules per plan are edited in **Gym SaaS Plan** in the desk. New gyms get the trial plan. When a trial or subscription ends, the gym becomes Past Due, and 7 days later it is Suspended (read-only) until it renews.
