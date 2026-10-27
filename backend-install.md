# Backend installation (Frappe / ERPNext v14, v15, v16)

[← Setup guide home](README.md)

The backend is the Frappe app `fitmanagr`. It holds all data and rules and runs the automation, and it exposes the REST API that every UI uses.

## 1. Prerequisites

You need a working Frappe bench for your version. To create a new one:

```bash
# pick ONE branch: version-14, version-15 or version-16
pip install frappe-bench
bench init --frappe-branch version-15 frappe-bench
cd frappe-bench
```

ERPNext is optional. To have it on the same site:

```bash
bench get-app erpnext --branch version-15
```

## 2. Get the app

The repository is private, so use the access token you were given. Match the branch to your Frappe version.

```bash
# Frappe v15 example; use version-14 or version-16 for other versions
bench get-app https://<github-token>@github.com/Satyabrata12017/fitmanagr-backend.git --branch version-15
```

> Using `bench get-app` with a token stores it in `apps/fitmanagr/.git/config`. After cloning, remove it:
> `git -C apps/fitmanagr remote set-url origin https://github.com/Satyabrata12017/fitmanagr-backend.git`

## 3. Install on a site

```bash
# new site (skip if you already have one)
bench new-site gym.example.com --admin-password '<strong-password>'

# with ERPNext (optional)
bench --site gym.example.com install-app erpnext

bench --site gym.example.com install-app fitmanagr
bench --site gym.example.com enable-scheduler
bench --site gym.example.com migrate
```

On install, FitManagr creates:

- Roles: Gym Super Admin, Gym Owner, Gym Manager, Gym Front Desk, Gym Trainer, Gym Member
- Default subscription plans (Starter, Growth, Pro) and settings
- Two Desk workspaces: **FitManagr** (for gym staff) and **FitManagr Platform** (for admins)

## 4. Optional extras

| Feature | What to do |
|---|---|
| Firebase login and push notifications | `bench pip install firebase-admin` (v15 and v16 only), then set the credentials path in **Gym SaaS Settings** |
| WhatsApp reminders | Install the WhatsApp Connect app and approve the message templates |
| Email (invoices, reminders, daily summary) | Set up an outgoing **Email Account** in Desk |
| SMS | Set the SMS provider keys in **Gym SaaS Settings** |

> Frappe v14 pins an older PyJWT that `firebase-admin` cannot use. On v14, leave Firebase off and use normal Frappe login. Everything else works.

## 5. Production

```bash
sudo bench setup production <linux-user>
bench setup nginx && sudo service nginx reload
sudo bench setup lets-encrypt gym.example.com
```

Members get a login the first time they sign in with OTP. Frappe allows only 60 new logins per hour per site by default, so raise the limit:

```bash
bench --site gym.example.com set-config -p throttle_user_limit 5000
```

If the frontend runs on a different domain, allow it:

```bash
bench --site gym.example.com set-config allow_cors '["https://app.example.com","https://localhost","capacitor://localhost"]'
```

## 6. Updating

```bash
cd apps/fitmanagr && git pull && cd ../..
bench --site gym.example.com migrate
bench build --app fitmanagr
bench restart
```

## 7. Check that it works

- Log in to `/app` as Administrator and open the **FitManagr** workspace.
- Go to **Gym → Set Up Gym** to create your first gym (see the [ERPNext Desk-only guide](erpnext-desk-only.md)).

## Next

- Use it from the ERPNext desk only → [ERPNext Desk-only setup](erpnext-desk-only.md)
- Add the owner and member apps → [Frontend installation](frontend-install.md)
- Build your own UI → [Custom UI on ERPNext](custom-ui.md)
