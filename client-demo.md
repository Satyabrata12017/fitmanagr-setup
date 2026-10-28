# Client demo script (ERPNext + apps)

[← Setup guide home](README.md) · [ERPNext guide with screenshots](erpnext-desk-only.md)

A 30-minute demo that shows the client FitManagr inside **their kind of system** (ERPNext), then the apps on top. Use it with the two decks: [Backend](FitManagr-Backend.pptx) and [Frontend](FitManagr-Frontend.pptx).

## Before the demo (10 minutes, once)

On a demo server or laptop with a v15 bench:

```bash
# 1. a clean demo site with ERPNext + FitManagr
bench new-site demo.local --admin-password admin --install-app erpnext --install-app fitmanagr
bench --site demo.local set-config fitmanagr_dev_otp 1          # shows OTP codes on screen (demo only)
bench --site demo.local set-config -p throttle_user_limit 5000

# 2. a demo gym with 60 members, staff, classes, payments and 3 months of history
bench --site demo.local execute fitmanagr.demo.seed

# 3. run it
bench --site demo.local serve --port 8000                       # ERPNext desk: http://localhost:8000
cd fitmanagr-frontend && npm run dev                            # apps: http://localhost:5174
```

Finish the ERPNext setup wizard once as Administrator (company, currency INR, country India).

| Login | Password | Use it for |
|---|---|---|
| `Administrator` | `admin` | Platform admin in the desk |
| `owner@demo.gym` | `Demo@12345` | Gym owner (desk and app) |
| `frontdesk@demo.gym` | `Staff@12345` | Front desk |
| `trainer1@demo.gym` | `Staff@12345` | Trainer |
| Member mobile `9800000001` | OTP shown on screen | Member app |

> These logins exist only on a site seeded with `fitmanagr.demo.seed`. Never seed demo data, or set `fitmanagr_dev_otp`, on a client's live site, and don't expose a demo server to the internet.

## The demo flow

| Min | Show | Where | Say |
|---|---|---|---|
| 0–3 | Backend deck slides 1–4 | Slides | "Your gym runs inside your ERPNext. One system, one login." |
| 3–6 | **Set Up Gym**, live | Desk as Administrator → Gym → Set Up Gym | "One click: owner login, branch, 13 plans, time slots, templates. All modules on, no expiry." |
| 6–9 | Owner workspace and Gym settings | Desk as `owner@demo.gym` → FitManagr | Point at the live counts, then GST and invoice settings, then Automation: "these run by themselves every night." |
| 9–14 | New member → Verify Mobile → Enrol → Collect Payment → Check In | Member form → **Actions** | "Front desk does everything from one menu. The GST invoice goes out on WhatsApp automatically." |
| 14–16 | Freeze a membership | Membership → Actions → Freeze | "The end date moves by itself." |
| 16–18 | Payment report view, group by Mode | Payment → Report view | "Standard ERPNext reports: filter, group and export to Excel." |
| 18–20 | Front desk and trainer logins | Desk | "Each role sees only its screens. Trainers can't see money." |
| 20–25 | Owner app: dashboard, members, check-in kiosk | http://localhost:5174 as owner (phone view) | "The same data on the owner's phone, live." |
| 25–28 | Member app | Mobile `9800000001` | "Members renew, book classes and see their diet without calling the desk." |
| 28–30 | Licensing slide and next steps | Backend deck slide 16 | Self-hosted licence, SaaS, or custom UI. |

## Questions clients often ask

| Question | Answer |
|---|---|
| Will it touch our ERPNext data? | No. FitManagr adds its own `Gym …` screens and changes nothing in ERPNext. |
| Which ERPNext version? | v14, v15 and v16 (one branch each). |
| Can we use only the desk? | Yes. The apps are optional and can be added later with no migration. |
| Can we have our own app design? | Yes. Every feature is in the REST API ([Custom UI](custom-ui.md)). |
| Multiple branches? | Yes. Front desk and trainers can be locked to a branch. |
| WhatsApp and SMS? | WhatsApp through WhatsApp Connect (Meta templates), SMS through MSG91 DLT templates. |
| GST invoices? | Yes. A PDF on every payment, with the gym's GSTIN, rate and SAC. |
| Data security? | Each gym's users see only their own gym's data. This is checked by automated tests on every release. |

## After a "yes": onboarding the client

1. Install on their ERPNext ([Backend installation](backend-install.md)).
2. Go through Parts 1–3 of the [ERPNext guide](erpnext-desk-only.md) with the owner (about 30 minutes).
3. Let the owner practise in Testing mode for a few days.
4. Run through the go-live checklist, then **Gym → Subscription → Go Live**.
