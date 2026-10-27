# Custom UI on top of ERPNext

[← Setup guide home](README.md)

Some customers want their own design (a branded website, a Flutter app, a kiosk screen) but keep ERPNext as the system of record. FitManagr is built for this. The backend is a normal Frappe app, and every feature is exposed as a REST API.

```
 Your UI (React / Flutter / Vue / website / kiosk)
        │  HTTPS + token
        ▼
 ERPNext / Frappe site  ──  fitmanagr app  (rules, automation, tenant security)
```

## Steps

1. **Install the backend** on the customer's ERPNext site ([guide](backend-install.md)).
2. **Create the gym** with **Gym → Set Up Gym** ([guide](erpnext-desk-only.md)).
3. **Create an API user.** For a staff UI, use a Gym Owner/Manager login. For a kiosk, use a Gym Front Desk login. Open **User → API Access → Generate Keys**.
4. **Call the API** from your UI:

```http
POST https://erp.example.com/api/method/fitmanagr.api.v1.members.check_in
Authorization: token <api_key>:<api_secret>
Content-Type: application/json

{ "member": "MEM-00012", "method": "Manual" }
```

5. **Allow your UI's domain** if it runs in a browser on another domain:

```bash
bench --site erp.example.com set-config allow_cors '["https://ui.example.com"]'
```

## What the API covers

| Area | Examples |
|---|---|
| Session | Login, current user, gym, role, enabled features |
| Members | Search, 360° profile, enrol/renew, collect payment, freeze/cancel, check-in, app invite |
| Operations | Classes and bookings, PT sessions, workout and diet plans, follow-ups, branches |
| Finance | Expenses, salary slips, P&L and collection reports |
| Insights | Dashboard numbers, revenue and attendance charts |
| Member app | Member login with OTP, plan card, bookings, payments, progress |
| Notifications | Bell, read/unread, push device tokens |

There are about 100 endpoints. The full reference (`docs/API.md`) ships with the backend repository to licensed customers.

You can also use standard Frappe REST on any FitManagr doctype:

```http
GET /api/resource/Gym Member?fields=["name","member_name","status","outstanding_amount"]&limit_page_length=50
```

## Security is built in

- Every request runs as the logged-in user, with Frappe roles and permissions applied.
- **Tenant isolation is automatic:** a user only ever sees their own gym's rows, even through `/api/resource`.
- Protected fields (status, licence, dues) can't be changed directly over REST.

## Mixing UIs

You can run the ERPNext desk, the FitManagr app and your custom UI **at the same time** on the same backend, for example:

- Accounts team: desk
- Front desk: FitManagr app on a tablet
- Members: your branded website
