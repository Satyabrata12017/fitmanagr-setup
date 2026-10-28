# ERPNext guide: setup, configuration and daily use

[← Setup guide home](README.md) · [Client demo script](client-demo.md)

This guide covers running FitManagr **entirely inside ERPNext**, without the separate app. Each step has a screenshot from a real ERPNext v15 site. The screens are the same on v14 and v16.

## Who does what

| Role in ERPNext | Who | Does |
|---|---|---|
| **System Manager** (platform admin) | Your IT person or the ERPNext admin | Installs FitManagr, sets up each gym, licence, go-live |
| **Gym Owner** | The gym owner | Gym settings, plans, branches, staff, all members and money |
| **Gym Manager** | Manager | Same as owner, but can only view the gym settings |
| **Gym Front Desk** | Reception | Members, enrolments, payments, check-in, leads (no deletes, no expenses or salaries) |
| **Gym Trainer** | Trainers | Classes, PT sessions, workout and diet plans, check-in (no money screens) |

Every gym user sees **only their own gym's data**. Front desk and trainers can also be limited to one branch.

---

## Part 1: Install and server configuration (System Manager)

1. Install the app on the ERPNext site. Use the branch that matches your version, as described in the [Backend installation](backend-install.md) guide:
   ```bash
   bench get-app https://<token>@github.com/Satyabrata12017/fitmanagr-backend.git --branch version-15
   bench --site erp.example.com install-app fitmanagr
   ```
2. Turn on the scheduler. Expiry, reminders and payroll run from it:
   ```bash
   bench --site erp.example.com enable-scheduler
   ```
3. Allow member logins. Members get a login the first time they sign in, and Frappe's default allows only 60 new logins per hour:
   ```bash
   bench --site erp.example.com set-config -p throttle_user_limit 5000
   ```
4. **Email:** go to **Settings → Email Account** and add an outgoing account. FitManagr emails invoices, staff password links and the owner's daily summary. Until it's set, ERPNext shows *"Please setup default outgoing Email Account"*.
5. **Optional:** WhatsApp Connect for WhatsApp reminders and invoices; SMS keys in **Gym SaaS Settings**.

---

## Part 2: Platform admin (System Manager)

### 2.1 The FitManagr Platform workspace

After install, the sidebar has **FitManagr Platform**, which shows all gyms, plans, invoices and support tickets.

![Platform workspace](images/erpnext/a1-platform-workspace.jpg)

### 2.2 Set up a gym

Open **Gym** and click **Set Up Gym**.

![Gym list with Set Up Gym](images/erpnext/a2-gym-list.jpg)

Enter the gym name, city, and the owner's name, email and mobile. The password is optional; without one, the owner uses *Forgot Password*.

![Set Up Gym dialog](images/erpnext/a3-set-up-gym.jpg)

One click creates:

- The gym, with a **self-hosted licence**: every module on, no expiry, no limits
- The owner's login (role Gym Owner)
- A **Main** branch and the owner's staff record
- 13 ready-made membership plans, 5 time slots, and diet and workout templates

![Gym created](images/erpnext/a4-gym-created.jpg)

### 2.3 Licence and go-live controls

The **Subscription** menu on the Gym form controls the licence.

| Action | When |
|---|---|
| **Self-Hosted Licence** | Turn a trial or SaaS gym into a perpetual licence |
| **Extend Trial / Expiry**, **Change Plan**, **Custom Pricing** | SaaS gyms |
| **Suspend / Activate** | Make a gym read-only, or reopen it |
| **Go Live** | Leave *Testing* mode once the owner has finished practising |

![Subscription menu](images/erpnext/a5-licence-menu.jpg)

![Go Live](images/erpnext/a6-go-live.jpg)

### 2.4 Plans and global settings (SaaS only)

**Gym SaaS Plan** and **Gym SaaS Settings** matter only if you sell FitManagr as a subscription. A direct-sale gym with a self-hosted licence ignores them.

![SaaS plans](images/erpnext/a7-saas-plans.jpg)

![SaaS settings](images/erpnext/a8-saas-settings.jpg)

---

## Part 3: Gym owner configuration (one time, about 15 minutes)

The owner logs in at `/app` with the email and password from Set Up Gym.

### 3.1 The FitManagr workspace

Live counts sit at the top, and every screen is grouped by job underneath.

![Owner workspace](images/erpnext/b01-owner-workspace.jpg)

### 3.2 Gym profile

Open **Gym** (Setup → Gym) and fill in the address, opening hours, time zone and logo. The logo appears on invoices and in the member app.

![Gym profile](images/erpnext/b02-gym-profile.jpg)

### 3.3 Invoice and GST

Set the **GSTIN**, the **GST rate** (5% for gym services, confirm with your CA), the **SAC code** (999723), the invoice prefix and terms, and the UPI ID printed on invoices.

![Invoice and GST](images/erpnext/b03-gym-invoice-gst.jpg)

### 3.4 Automation

These switches control what FitManagr does on its own:

| Setting | What it does |
|---|---|
| Renewal Reminder Days (`7,3,1`) | Reminders before a membership ends |
| Auto Expire Memberships | Marks members Expired at midnight |
| Block Check-in For Expired Members, or When Dues Are Pending | Door rules |
| Verify Member Mobile Before Collecting Payment | Receipts go to a verified number |
| Check Time Slots At Check-in | Off-peak plans only allow entry in their slot |
| Email / WhatsApp / SMS / Push | Channels for every message |
| Send App Invite, Birthday Wishes, Daily Summary, Lead Alerts | Engagement |
| Payroll & Expenses | Salary day, automatic salary slips, expense vouchers to payees |

![Automation settings](images/erpnext/b04-gym-automation.jpg)

### 3.5 Membership plans

There are 13 plans ready to edit: monthly to yearly, PT packs, couple, student, off-peak, day pass and combos. Change the prices to match the gym.

![Plans](images/erpnext/b05-plans.jpg)

Each plan has its price, joining fee (charged only the first time), duration, PT sessions, diet and workout inclusion, class access, an optional time slot, and the freeze days allowed.

![Plan form](images/erpnext/b06-plan-form.jpg)

### 3.6 Branches

A **Main** branch is created for you. Add more branches if the gym has several locations.

![Branches](images/erpnext/b07-branches.jpg)

### 3.7 Staff and their logins

Go to **Staff → New** and fill in:

- **Role:** Manager, Front Desk or Trainer
- **Create App Login:** emails the staff member a password link
- **Branch:** restricts front desk and trainers to one branch
- **Salary and PT commission:** used for payroll
- **Trainer profile:** shown to members in the app

![New staff](images/erpnext/b08-new-staff.jpg)

![Staff saved](images/erpnext/b09-staff-saved.jpg)

---

## Part 4: Daily front desk work

### 4.1 Add a member

Go to **Member → New** and enter the name, mobile, email and gender. The gym and branch fill in automatically.

![New member](images/erpnext/b10-new-member.jpg)

![Member saved](images/erpnext/b11-member-saved.jpg)

### 4.2 The Actions menu

Everything for a member is in one menu: **Enrol / Renew, Collect Payment, Check In, Send App Invite, Verify Mobile**.

![Member actions](images/erpnext/b12-member-actions.jpg)

### 4.3 Verify the mobile

**Verify Mobile** sends a code by WhatsApp or SMS. Type the code the member reads out. Payments need a verified number, which you can switch off in Automation.

![Verify mobile](images/erpnext/b13-verify-mobile.jpg)

### 4.4 Enrol or renew

Choose the plan, a discount if any, and the amount paid now. A renewal starts the day after the current plan ends. The joining fee is added automatically for a first-time member.

![Enrol](images/erpnext/b14-enrol.jpg)

The member becomes **Active**, with the plan, end date and **outstanding** amount filled in.

![Member enrolled](images/erpnext/b15-member-enrolled.jpg)

### 4.5 Collect the balance

**Collect Payment** suggests the outstanding amount. The payment goes to the oldest due, and overpayment is blocked. A GST invoice is created for every payment.

![Collect payment](images/erpnext/b16-collect-payment.jpg)

### 4.6 Check in

**Check In** records the visit. Expired or frozen members are blocked, and repeat scans within 30 minutes are ignored.

![Checked in](images/erpnext/b17-checked-in.jpg)

### 4.7 Freeze, unfreeze or cancel

Open the membership and use **Actions → Freeze**. The end date moves out by the frozen days.

![Membership actions](images/erpnext/b18-membership-actions.jpg)

![Freeze](images/erpnext/b19-freeze.jpg)

---

## Part 5: Money and reports

Use the standard ERPNext list and report views: filter, group by, add columns, export to Excel and print.

| Screen | Shows |
|---|---|
| **Payment** (Report view) | Collections by date, mode and member |
| **Invoice** | GST invoices, one per payment |
| **Check In** | Attendance log |
| **Staff** | Team and roles |

![Payments report](images/erpnext/b20-payments-report.jpg)

![Invoices](images/erpnext/b21-invoices.jpg)

![Check-ins](images/erpnext/b22-check-ins.jpg)

![Staff list](images/erpnext/b23-staff-list.jpg)

---

## Part 6: What staff see

**Front desk:** the same workspace without salaries, expenses or deletes.

![Front desk workspace](images/erpnext/c1-front-desk-workspace.jpg)

![Front desk members](images/erpnext/c2-front-desk-members.jpg)

**Trainer:** members, classes, PT sessions, and workout and diet plans. No payments, invoices or expenses.

![Trainer workspace](images/erpnext/c3-trainer-workspace.jpg)

A trainer who opens a money screen gets **Not permitted**:

![Trainer blocked](images/erpnext/c4-trainer-no-access.jpg)

### Permission summary

| Screen | Owner | Manager | Front Desk | Trainer |
|---|---|---|---|---|
| Members, Memberships | Full | Full | Add / edit | View |
| Payments | Full | Full | Add | — |
| Invoices | Full | Full | View | — |
| Check-in | Full | Full | Add | Add |
| Leads, Follow-ups | Full | Full | Add / edit | — |
| Classes, PT, Workout, Diet | Full | Full | View / book | Add / edit |
| Expenses, Salary slips | Full | Full | — | — |
| Staff, Plans, Branches | Full | Full | View | View |
| Gym settings | Edit | View | View | View |

---

## Go-live checklist

- [ ] Scheduler enabled and the outgoing email account set up
- [ ] `throttle_user_limit` raised
- [ ] Gym profile, logo, GSTIN, GST rate and invoice terms filled in
- [ ] Plan prices checked; unused plans have *Available For Sale* turned off
- [ ] Staff added with logins; front desk and trainers given a branch
- [ ] Automation channels chosen (Email / WhatsApp / SMS)
- [ ] The owner has practised; the System Manager clicks **Gym → Subscription → Go Live**

## FAQ

**Does FitManagr change ERPNext?** No. It adds its own `Gym …` doctypes and doesn't modify ERPNext.

**Can staff hide the ERPNext modules they don't use?** Yes. Set a **Module Profile** on the staff user (User → Module Profile) to show only *Gym Core, Gym Membership, Gym Operations, Gym Support*.

**Can gym payments post to ERPNext accounts?** FitManagr keeps its own payments, invoices and expenses. Posting them to ERPNext accounts can be added as a custom build.

**Will the gym ever expire?** No. Self-hosted gyms have no expiry date, and the daily licence check skips them.
