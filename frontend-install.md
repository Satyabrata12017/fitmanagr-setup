# Frontend installation (web app + Android + iOS)

[← Setup guide home](README.md)

The frontend is one React app with three experiences. Each person sees the one that matches their login:

| Login | Who | Experience |
|---|---|---|
| Email/mobile + password | Gym owner and staff | **Owner app**: dashboard, members, billing, check-in, kiosk, leads, classes, staff, expenses, reports |
| Mobile + one-time code | Gym members | **Member app**: plan card, QR check-in, classes, diet and workout plans, payments, progress |
| Platform admin | You | **Admin console**: all gyms, plans, pricing, support |

It runs as an installable web app (PWA) and is also packaged as Android and iOS store apps. It works against **any** site where the FitManagr backend is installed, including your existing ERPNext.

## 1. Prerequisites

- The [backend](backend-install.md) installed on a site such as `https://gym.example.com`
- Node.js 20 or 22, plus npm
- For Android builds: JDK 21 and the Android SDK. For iOS: a Mac with Xcode, or the included GitHub Actions workflow.

## 2. Get the code

```bash
git clone https://<github-token>@github.com/Satyabrata12017/fitmanagr-frontend.git
cd fitmanagr-frontend
npm ci
```

## 3. Configure

```bash
cp .env.example .env
```

| Setting | Meaning |
|---|---|
| `VITE_APP_NAME` | Brand name shown in the app (white-label per customer) |
| `VITE_API_BASE` | Backend URL, e.g. `https://gym.example.com`. Leave empty when the app and backend share one domain |
| `VITE_AUTH_MODE` | `frappe` (normal Frappe login, recommended) or `firebase` (SaaS mode, see [Cloud](cloud-saas.md)) |
| `VITE_FIREBASE_*` | Only for Firebase login or web push |

If the app runs on a different domain from the backend, allow it on the backend:

```bash
bench --site gym.example.com set-config allow_cors '["https://app.example.com","https://localhost","capacitor://localhost"]'
```

## 4. Run locally

```bash
# FRAPPE_DEV_URL / FRAPPE_DEV_SITE in .env point the dev proxy at your local bench
npm run dev        # http://localhost:5174
```

## 5. Build and host the web app

```bash
VITE_API_BASE=https://gym.example.com npm run build
# upload dist/ to any static host: nginx, Firebase Hosting, Netlify, S3 + CloudFront
```

Example nginx block for `app.example.com`:

```nginx
server {
  server_name app.example.com;
  root /var/www/fitmanagr/dist;
  location / { try_files $uri /index.html; }
  location = /sw.js { add_header Cache-Control "no-cache"; }
}
```

Staff can install it from the browser ("Add to Home screen"). It works full-screen on phones and tablets.

## 6. Android app (Play Store)

```bash
API_URL=https://gym.example.com VERSION_NAME=1.0.0 VERSION_CODE=1 ./scripts/build-mobile.sh
```

- The first run creates `keys/upload.jks`, the signing key. **Back it up.** Every future update must be signed with it, and it is never committed to git.
- Output: `dist-mobile/fitmanagr-1.0.0.aab` to upload to Play Console, and an `.apk` for testing on a phone.
- For push notifications, put `google-services.json` in `android/app/`.
- App id: `com.fitmanagr.app`. Change `appId` in `capacitor.config.json` for a white-label build.

## 7. iOS app (App Store)

- **On a Mac:** run `npx cap sync ios && npx cap open ios`, then Archive in Xcode.
- **Without a Mac:** run the **iOS app** GitHub Actions workflow. Add the Apple signing secrets listed at the top of `.github/workflows/ios.yml`.

## 8. White-label for a customer

1. Set `VITE_APP_NAME` and replace the logo in `brand/`, then run `npm run icons`.
2. Change `appId` (Android/iOS) and `APP_NAME` / `PACKAGE_ID` in `twa/twa-config.env` if you use the TWA wrapper.
3. Build as above, pointing `VITE_API_BASE` at the customer's server.

## 9. Updating

```bash
git pull && npm ci && npm run build
```

Upload the new `dist/`. Installed PWAs show an "Update available" prompt.
