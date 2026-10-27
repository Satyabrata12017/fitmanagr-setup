# Hosting on Frappe Cloud

[← Setup guide home](README.md)

FitManagr is a private app, so customers on Frappe Cloud install it on a **private bench** (bench group), not from the public Marketplace.

## Install on a Frappe Cloud private bench

1. In the Frappe Cloud dashboard, create or open a **Bench Group** with your version (v14, v15 or v16) and ERPNext if needed.
2. Go to **Apps → Add App → GitHub**. Authorise the Frappe Cloud GitHub app for the `Satyabrata12017` account. Allow access to the private `fitmanagr-backend` repository.
3. Choose the branch that matches the bench version: `version-14`, `version-15` or `version-16`.
4. **Deploy** the bench group.
5. Open the site → **Apps → Install App → fitmanagr**.
6. Open the site desk → **Gym → Set Up Gym** ([details](erpnext-desk-only.md)).

Updates: push to the branch, then click **Deploy** on the bench group.

## About the public Marketplace

The Frappe Cloud Marketplace lists only **open-source apps from public GitHub repositories** (MIT or GPL-compatible license). FitManagr is licensed privately, so it is distributed through private benches and direct installation instead. If a public listing is wanted later, the backend has a `marketplace/` folder with the listing text, logo and a checklist.
