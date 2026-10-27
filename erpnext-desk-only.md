# ERPNext Desk-only setup (no separate frontend)

[← Setup guide home](README.md)

Some customers already run ERPNext and want gym management in the same system, with the same login and no separate app. FitManagr supports this fully. Every gym record has a form, a list, permissions and a workspace in the Frappe desk (`/app`).

## What you get in the desk

| Workspace | Who sees it | What is inside |
|---|---|---|
| **FitManagr** | Gym Owner, Manager, Front Desk, Trainer | Members, Memberships, Payments, Check-ins, Leads, Classes, PT sessions, Workout and Diet plans, Staff, Salary, Expenses, Branches, Plans, Holidays |
| **FitManagr Platform** | System Manager, Gym Super Admin | All gyms, licences, plans, settings, support tickets |

The automation runs the same way as with the app: nightly membership expiry, renewal and birthday reminders (email, WhatsApp, SMS), dues, payroll and the owner's daily summary.

## Step by step

### 1. Install the backend

Follow the [Backend installation](backend-install.md) guide on your ERPNext site. Use the branch that matches your ERPNext version:

| ERPNext | Branch |
|---|---|
| v14 | `version-14` |
| v15 | `version-15` |
| v16 | `version-16` |

### 2. Create the gym (one time)

**From the desk (easiest):**

1. Log in as a **System Manager**.
2. Open **Gym** (search "Gym List" in the awesome bar).
3. Click **Set Up Gym**.
4. Enter the gym name, city, owner name, owner email, owner mobile and, optionally, a password.
5. Click **Create**.

**From the terminal:**

```bash
bench --site erp.example.com execute fitmanagr.services.self_hosted.setup_gym \
  --kwargs '{"gym_name": "Power House Gym", "owner_name": "Ravi Kumar", "email": "owner@powerhouse.in", "mobile": "9876543210", "city": "Pune"}'
```

Either way, FitManagr creates:

- The **Gym** with a **self-hosted licence**: every module on, no expiry, no member/branch/staff limits
- The owner's login (role **Gym Owner**)
- A **Main** branch and the owner's staff record
- Starter membership plans, time slots and workout/diet templates

> To convert a gym that already exists (for example, one that started as a trial), open the Gym and click **Subscription → Self-Hosted Licence**.

### 3. Add staff

Open **Gym Staff → New** and set:

- **Role**: Manager, Front Desk or Trainer
- **Branch**, to restrict front desk and trainers to one branch
- **Create Login**: sends the staff member a password link by email

Each staff login sees only its own gym's data, and its menus follow its role.

### 4. Daily use in the desk

| Task | Where |
|---|---|
| Add a member | Gym Member → New |
| Enrol or renew | Open the member → **Actions → Enrol / Renew** (plan, discount, payment in one step) |
| Take a payment | Open the member → **Actions → Collect Payment** (goes to the oldest due) |
| Mark attendance | Open the member → **Actions → Check In** (expired or frozen members are blocked) |
| Send the member app link | Open the member → **Actions → Send App Invite** |
| Freeze, unfreeze or cancel | Open the membership → **Actions → Freeze / Unfreeze / Cancel Membership** |
| Leads and follow-ups | Gym Lead, Gym Follow Up |
| Classes and bookings | Gym Class, Gym Class Booking |
| Expenses and salary | Gym Expense, Gym Salary Slip |
| Reports | Report view on any list (group-by, filters, export to Excel) |

### 5. Optional: give members the app later

The desk-only setup uses the same backend as the apps. If the gym later wants the member app or the owner mobile app, add the [Frontend](frontend-install.md) and point it at this site. Nothing needs to be migrated.

## Multiple gyms on one ERPNext

Run **Set Up Gym** once per gym. Each gym's users see only that gym's data. System Managers see all gyms.

## FAQ

**Does FitManagr change ERPNext?** No. It adds its own doctypes (all named `Gym …`) and doesn't modify ERPNext doctypes.

**Can I post gym payments to ERPNext accounts?** FitManagr keeps its own payments, invoices and expenses. To post them to ERPNext, create a Journal Entry or Payment Entry in a custom app or server script that runs on Gym Payment submit. Ask us if you need this built.

**Will the gym ever expire?** No. Self-hosted gyms have no expiry date, and the daily licence check skips them.
