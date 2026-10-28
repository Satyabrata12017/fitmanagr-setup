# Production server (one domain, HTTPS, Android download)

This guide puts the whole of FitManagr on one Ubuntu server under one domain:

| Path | Serves |
|---|---|
| `https://your-domain/` | FitManagr web app (owners, staff, members, admin console) |
| `https://your-domain/frontend/login` | Web app login (owners, staff, members, platform admin) |
| `https://your-domain/app` | ERPNext / Frappe Desk |
| `https://your-domain/login` | ERPNext / Frappe Desk login |
| `https://your-domain/api/` | REST API used by the web and mobile apps |
| `https://your-domain/downloads/` | Android APK download and iPhone install steps |
| `https://your-domain/shop/<store-address>` | A gym's online store (set in the gym app → Store → Settings) |

Tested on Ubuntu 24.04 with Frappe and ERPNext v15. The scripts are in the `deploy/` folder of the private `fitmanagr` repository.

## 1. Server

| Item | Minimum |
|---|---|
| CPU / RAM | 2 vCPU, 4 GB RAM (the script adds 4 GB swap) |
| Disk | 40 GB or more |
| OS | Ubuntu 24.04 LTS |

**Firewall (AWS Security Group or similar):** allow **inbound** TCP **22** (SSH, your IP only), **80** (HTTP) and **443** (HTTPS) from anywhere. Leave outbound open.

## 2. DNS

At your DNS provider add an **A record**: `your-domain` → the server's public IP. Wait until `nslookup your-domain` returns that IP.

## 3. Install

Copy the `deploy/` folder and a bundle of the backend (`git bundle create fitmanagr-backend.bundle version-15`) to the server, then:

```bash
sudo bash 01-system.sh        # packages, MariaDB, Redis, Node, wkhtmltopdf, swap, bench
sudo bash 02-bench.sh         # bench, ERPNext, FitManagr, site
sudo bash 03-web.sh your-domain   # supervisor, nginx, HTTPS certificate, domain agent
```

Generated passwords (database root, Administrator) are saved in `/root/fitmanagr-secrets.env`, readable only by root.

From your computer, build and upload the web app and the Android APK:

```bash
SERVER=ubuntu@your-server SSH_KEY=~/.ssh/key.pem ./deploy-web.sh path/to/FitManagr.apk
```

Finally log in to `https://your-domain/app` as Administrator and finish the ERPNext setup wizard (company, currency, country).

## 4. Change the domain later (no command line)

1. Point the new domain's A record to the same server IP.
2. Desk → **FitManagr Platform → Domain Settings**.
3. Type the domain, click **Check DNS**, then **Apply Domain** (tick *Make primary* if it should become the main address).

The server issues a free Let's Encrypt certificate, adds the domain to nginx and, for the primary domain, updates the links used in emails, invoices and app invites. Old domains keep working, so installed Android apps are not affected. Certificates renew automatically. Each domain in the list has **Make Primary** and **Remove** buttons.

## 5. Mobile apps

| Platform | How users install |
|---|---|
| Android | `https://your-domain/downloads/` → **Download for Android**. Publish the `.aab` build on Google Play when ready. |
| iPhone / iPad | Open the site in Safari → **Share → Add to Home Screen**. A native App Store app needs an Apple Developer account. |

## 6. Releases and updates

Everything is done from the **super admin app → Releases** (or Desk → FitManagr Release).

**One-time setup** (super admin app → Releases → **Settings**)
1. **GitHub**: a fine-grained token with *Contents: Read-only* on the backend and frontend repositories (shown masked, eye button to reveal).
2. **Google Play** (optional, any time later): package name, track (internal / alpha / beta / production) and the Play Console service-account JSON key.
3. **Servers** (optional): add every other FitManagr server with its URL and a System Manager API key. Each server downloads releases from GitHub itself.
4. For automatic Android builds add these secrets to the frontend repository: `API_URL`, `ANDROID_KEYSTORE_BASE64`, `ANDROID_KEYSTORE_PASSWORD`, `ANDROID_KEY_PASSWORD`.

**Every release**
1. Publish a **GitHub Release** on the backend or frontend repository with a tag like `v1.2.0`. The release title and description become the release notes users see. For a new mobile version, bump `package.json` first; the Android workflow attaches `FitManagr.apk` and the `.aab`.
2. The server picks it up within 15 minutes (or click **Check GitHub**). Platform admins get a bell notification.
3. Open the release, review the notes, choose where to install it (**this server**, other servers) and optionally **Publish to Google Play**, then click **Install**. The log and each server's status update live.
4. Users see an **Update** button in the web app, desk and Android app. Tick **Users must update** to block older Android versions.

## 7. Manual updates (command line)

```bash
SERVER=ubuntu@your-server SSH_KEY=~/.ssh/key.pem ./update-backend.sh version-15   # backend + migrate
SERVER=ubuntu@your-server SSH_KEY=~/.ssh/key.pem ./deploy-web.sh                  # web app
```

## Troubleshooting

| Symptom | Check |
|---|---|
| Site does not open from outside | Inbound 80/443 allowed in the cloud firewall |
| Apply Domain says DNS is wrong | The A record points to the server IP shown in Domain Settings |
| 502 Bad Gateway | `sudo supervisorctl status`: all FitManagr processes RUNNING |
| Emails or links show the old domain | Make the new domain primary in Domain Settings |
| Release stays Queued | `systemctl status fitmanagr-release.path`; the log is in `/var/lib/fitmanagr/release/logs/` |
| Check for Updates says token rejected | Create a new token with Contents: Read-only and save it in Release Settings |

Support: satyabrata12017@gmail.com
