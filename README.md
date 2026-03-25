# Internal Phishing Simulation Project

> A structured, ethical phishing simulation exercise conducted to assess and improve employee security awareness within an organisation.

---

## Project Overview

This project documents the end-to-end setup and execution of an internal phishing simulation using **GoPhish** — an open-source phishing framework. The goal is to measure employee susceptibility to phishing attacks, identify vulnerable groups, and drive targeted security awareness training.

This is **not** a malicious project. All activities were conducted with full executive approval, within a controlled environment, and with the intent to educate targeted audience.

---

## 🎯 Objectives

- Establish a baseline phishing susceptibility rate across the organisation
- Identify departments or roles that require additional security training
- Simulate a realistic CEO impersonation (BEC) attack scenario
- Redirect employees who click to an immediate awareness training resource
- Improve overall organisational security posture

---

## 🛠️ Tools & Infrastructure

| Tool | Purpose |
|------|---------|
| [GoPhish](https://getgophish.com/) | Phishing simulation framework |
| Microsoft Azure (Ubuntu VM) | Server hosting |
| Gmail + App Password | SMTP sending |
| DuckDNS | Free dynamic DNS for phishing URL |
| Google Phishing Quiz | Redirect/awareness training landing |

---

## 📋 Prerequisites

- An Azure VM (or any Linux server) running Ubuntu
- A Gmail account with 2FA enabled
- A DuckDNS account (free)
- GoPhish binary downloaded
- Management approval ✅

---

## ⚙️ Setup Guide

### 1. Download & Run GoPhish

```bash
# Download GoPhish
wget https://github.com/gophish/gophish/releases/latest/download/gophish-v0.12.1-linux-64bit.zip
unzip gophish-v0.12.1-linux-64bit.zip
chmod +x gophish

# Run GoPhish in the background (stays alive after terminal closes)
sudo nohup ./gophish &
```

> On first run, GoPhish will print a one-time admin password in the logs. Save it immediately.

---

### 2. Configure `config.json`

```json
{
    "admin_server": {
        "listen_url": "127.0.0.1:3333",
        "use_tls": true,
        "cert_path": "gophish_admin.crt",
        "key_path": "gophish_admin.key",
        "trusted_origins": []
    },
    "phish_server": {
        "listen_url": "0.0.0.0:80",
        "use_tls": false,
        "cert_path": "example.crt",
        "key_path": "example.key"
    },
    "db_name": "sqlite3",
    "db_path": "gophish.db",
    "migrations_prefix": "db/db_",
    "contact_address": "you@yourcompany.com",
    "logging": {
        "filename": "gophish.log",
        "level": "info"
    }
}
```

> The admin panel is bound to `127.0.0.1` for security. Access it via SSH tunnel.

---

### 3. Access Admin Panel via SSH Tunnel

Run this on the **local machine** (not the server):

```bash
ssh -L 3333:127.0.0.1:3333 username@<your-server-public-ip>
```

Then open your browser and navigate to:
```
https://127.0.0.1:3333
```

---

### 4. Set Up Sending Profile (Gmail SMTP)

Before configuring GoPhish, generate a Gmail App Password:

1. Enable 2-Step Verification on the Gmail account
2. Go to **Security → App Passwords**
3. Generate a password for "GoPhish"

Then in GoPhish **Sending Profiles**:

```
Name:      Simulation Mailer
From:      [Display Name] <yourgmail@gmail.com>
Host:      smtp.gmail.com:465
Username:  yourgmail@gmail.com
Password:  [16-character App Password, no spaces]

☑️ Ignore Certificate Errors
```

---

### 5. Set Up DuckDNS (Free Domain)

1. Go to [duckdns.org](https://www.duckdns.org) and sign in
2. Create a subdomain (e.g., `yourcompany-hr.duckdns.org`)
3. Enter your Azure server's public IP
4. Use the DuckDNS URL as your GoPhish campaign URL:

```
http://yourcompany-hr.duckdns.org
```

---

### 6. Create Landing Page

In GoPhish **Landing Pages → New Page**:
- Switch to **HTML/Source mode** and paste your custom login page HTML
- Enable **Capture Submitted Data** and **Capture Passwords** OR NOT (depending on the scope of approval)
- Set **Redirect URL** to:

```
https://phishingquiz.withgoogle.com/
```

> Employees who submit credentials are immediately redirected to Google's Phishing Quiz for instant awareness training.

---

---

### 8. Launch Campaign

In GoPhish **Campaigns → New Campaign**:

```
Name:             2026 Salary Increment Simulation
Email Template:   [your template]
Landing Page:     [your landing page]
URL:              http://yourcompany-hr.duckdns.org
Launch Date:      [your chosen date]
Sending Profile:  CEO Simulation Mailer
Groups:           [your employee target group]
```

---

## 📊 Metrics Tracked

GoPhish automatically tracks the following per employee:

| Metric | Description |
|--------|-------------|
| 📨 Email Sent | Email successfully delivered |
| 👁️ Email Opened | Employee opened the email |
| 🖱️ Link Clicked | Employee clicked the phishing link |
| 📝 Credentials Submitted | Employee entered data on landing page |
| 🚩 Email Reported | Employee reported the email as suspicious |

---

## 🔐 Ethical Guidelines

- ✅ Full written executive approval obtained before simulation
- ✅ HR and Legal informed prior to launch
- ✅ IT/Helpdesk notified to prevent false incident escalation
- ✅ No credentials stored externally — GoPhish local DB only
- ✅ Results reported in aggregate — no individual shaming
- ✅ Employees who clicked redirected immediately to awareness training
- ✅ Follow-up training provided to vulnerable groups

---

## 📁 Project Structure

```
phishing-simulation/
│
├── config.json          # GoPhish configuration
├── gophish.log          # Runtime logs
├── gophish.db           # SQLite results database
├── landing-page.html    # Custom phishing landing page
├── email-template.html  # Phishing email body
└── README.md            # This file
```

---

## ⚠️ Disclaimer

This project was conducted solely for **internal security awareness purposes** with full organisational approval. Phishing simulations without proper authorisation are illegal and unethical. Do not replicate this outside of an authorised engagement.

---

## 📚 Resources

- [GoPhish Documentation](https://docs.getgophish.com/)
- [Google Phishing Quiz](https://phishingquiz.withgoogle.com/)
- [DuckDNS](https://www.duckdns.org/)
- [SANS Security Awareness](https://www.sans.org/security-awareness-training/)

---

*Built as part of an internal cybersecurity awareness initiative.*
