# FitManagr Setup Guide

FitManagr is gym and fitness-centre management software built on the Frappe Framework. It runs on its own or next to ERPNext, on **Frappe v14, v15 and v16**.

This page covers installation and setup only. The FitManagr source code is private. Licensed customers get repository access from Satya.

## Pick your setup

| You want | Setup | Guide |
|---|---|---|
| Gym management inside your existing **ERPNext** (no separate app) | Backend only, used from the ERPNext desk | [ERPNext guide with screenshots](erpnext-desk-only.md) |
| Show FitManagr to a client | Demo site with sample data, plus a 30-minute script | [Client demo script](client-demo.md) |
| The full FitManagr experience: owner app, member app, kiosk and mobile apps | Backend + Frontend | [Backend](backend-install.md) → [Frontend](frontend-install.md) |
| Your **own custom UI** (website, Flutter, React) on top of ERPNext | Backend + your UI using the REST API | [Custom UI on ERPNext](custom-ui.md) |
| Run FitManagr as a multi-gym **SaaS** with self-signup and online billing | Backend + Frontend + Cloud | [SaaS / Cloud setup](cloud-saas.md) |
| Hosting on **Frappe Cloud** | Backend on a private bench | [Frappe Cloud](frappe-cloud.md) |

## The parts

| Part | Repository (private) | What it is |
|---|---|---|
| Backend | `fitmanagr-backend` | Frappe app `fitmanagr`: data, rules, automation, REST API, Desk workspace |
| Frontend | `fitmanagr-frontend` | Web app (PWA) for owners, staff and members, plus Android/iOS apps |
| Cloud | `fitmanagr-cloud` | Optional Firebase services: signup, subscription billing, push |

## Supported versions

| Frappe / ERPNext | Backend branch | Python | Node | MariaDB |
|---|---|---|---|---|
| v14 | `version-14` | 3.10 | 16 or 18 | 10.6+ |
| v15 | `version-15` | 3.11 / 3.12 | 18 or 20 | 10.6+ |
| v16 | `version-16` | 3.14 | 24 | 10.6+ / 11.x |

ERPNext is **optional**. FitManagr needs only Frappe, and it installs cleanly on a site that already has ERPNext.

## Presentations

| Deck | For | Download |
|---|---|---|
| **FitManagr for ERPNext (Backend)** | ERPNext users, IT teams: Desk-only setup, every backend feature, automation, security, API, install, licensing | [FitManagr-Backend.pptx](FitManagr-Backend.pptx) |
| **FitManagr Apps (Frontend)** | Gym owners: owner/staff app, member app, check-in, payments, notifications, mobile apps, admin console | [FitManagr-Frontend.pptx](FitManagr-Frontend.pptx) |

## Getting access

1. Ask Satya for access. You get read access to the private repositories, or a deploy token.
2. Clone or `bench get-app` using that access, as shown in each guide.

Support: satyabrata12017@gmail.com
