# 🌌 EquinoxAI

> **Predictive Market Intelligence powered by 53 years of historical trading data (1964–2017), Linear Regression via ONNX Runtime, and Serverless Edge Architecture.**

[![Repository](https://img.shields.io/badge/GitHub-adamcodetime%2FVERITAS__HACKATHON-191970?style=for-the-badge&logo=github)](https://github.com/adamcodetime/VERITAS_HACKATHON)
[![Edge Runtime](https://img.shields.io/badge/Backend-Cloudflare_Workers-44A1A0?style=for-the-badge&logo=cloudflare)](https://workers.cloudflare.com/)
[![Inference](https://img.shields.io/badge/AI_Engine-ONNX_Runtime-78CDD7?style=for-the-badge)](https://onnx.ai/)
[![Database](https://img.shields.io/badge/Database-Cloudflare_D1-353531?style=for-the-badge)](https://developers.cloudflare.com/d1/)

---

## 📖 Table of Contents
1. [Overview](#-overview)
2. [System Architecture](#-system-architecture)
3. [Frontend Architecture & Design System](#-frontend-architecture--design-system)
4. [Backend API & Edge Infrastructure](#-backend-api--edge-infrastructure)
5. [Security & Authentication Model](#-security--authentication-model)
6. [Getting Started & Local Development](#-getting-started--local-development)
7. [Engineering Write-Up](#-engineering-write-up)

---

## 🚀 Overview

**EquinoxAI** is a full-stack predictive financial platform trained on historical stock market trades spanning **1964 to 2017**. Rather than relying on slow, monolithic server setups or opaque deep-learning black boxes, EquinoxAI pairs an explainable **Linear Regression** model exported to **ONNX** with a globally distributed **Cloudflare Workers** backend and a high-contrast, responsive web interface.

### Core Pillars
* **Historical Depth (1964–2017):** Calibrated across five decades of market cycles, bull runs, and recessions.
* **Sub-Second Edge Inference:** Exported ONNX weights executed at the edge eliminate cold-start bottlenecks.
* **Zero-Friction User Experience:** High-contrast visual hierarchy designed to keep analysts and curious users focused on data without UI clutter.

---

## 🏗️ System Architecture

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                            CLIENT / FRONTEND                            │
│  • High-Contrast UI (#FFFFFA / #191970)                                 │
│  • Interactive CTA ("Try It Yourself" -> /try) & Source Redirect        │
│  • Embedded Demo Walkthrough & Team Footer                              │
└───────────────────────────────────┬─────────────────────────────────────┘
                                    │ HTTPS / CORS-Validated Fetch
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      CLOUDFLARE WORKERS EDGE API                        │
│  • Strict Origin & Loopback CORS Enforcement                            │
│  • PBKDF2-SHA256 Auth + HttpOnly Session Cookies                        │
│  • Rate-Limited Email Verification (EmailJS / Resend)                   │
└───────────────┬─────────────────────────────────────────┬───────────────┘
                │                                         │
                ▼                                         ▼
┌───────────────────────────────┐         ┌───────────────────────────────┐
│     CLOUDFLARE D1 (SQLite)    │         │     ONNX INFERENCE ENGINE     │
│  • users & sessions           │         │  • Linear Regression Weights  │
│  • verification_email_limits  │         │  • Historical Dataset         │
│  • messages (community feed)  │         │    (1964–2017 Market Trades)  │
└───────────────────────────────┘         └───────────────────────────────┘
```

---

## 🎨 Frontend Architecture & Design System

The EquinoxAI frontend is engineered to balance visual calm with analytical precision, inviting users to explore the platform and test predictions effortlessly.

### 1. Color Palette & Visual Hierarchy
Every component adheres to a strict 5-token design system:

| Token | Hex Code | Swatch | Role & Usage |
| :--- | :--- | :---: | :--- |
| **Background** | `#FFFFFA` | ![#FFFFFA](https://via.placeholder.com/16/FFFFFA/000000?text=+) | Soft ivory baseplate that reduces eye strain during prolonged data inspection. |
| **Primary / Text** | `#191970` | ![#191970](https://via.placeholder.com/16/191970/FFFFFF?text=+) | Midnight Blue used for brand typography, logo mark, and primary headers. |
| **Accent 1** | `#44A1A0` | ![#44A1A0](https://via.placeholder.com/16/44A1A0/FFFFFF?text=+) | Deep Verdigris Teal for primary interactive calls-to-action and active states. |
| **Accent 2** | `#78CDD7` | ![#78CDD7](https://via.placeholder.com/16/78CDD7/000000?text=+) | Cyan Mist used for hover illumination, highlights, and secondary visual accents. |
| **Contrast Divs** | `#353531` | ![#353531](https://via.placeholder.com/16/353531/FFFFFF?text=+) | Charcoal Slate for structural containers, cards, and footer contrast blocks. |

### 2. Homepage Layout & Interactive Components
* **Hero Baseplate & Project Explanation:** Immediately communicates EquinoxAI’s mission, dataset timeframe (1964–2017), and ONNX speed advantage upon page load.
* **Top Navigation Action Bar:**
  * **Project Source Button:** Direct external link to [`adamcodetime/VERITAS_HACKATHON`](https://github.com/adamcodetime/VERITAS_HACKATHON) for full code transparency.
  * **"Try It Yourself" CTA Button:** Styled with `#44A1A0` / `#78CDD7` accents. On hover, it triggers a multi-layered CSS transition featuring an outer glow, light-sweep shine effect, and subtle scale transform before routing users to the `/try` sandbox environment.
* **Embedded Demo Walkthrough:** Positioned below the fold to showcase real-time inference and platform navigation without crowding the hero viewport.
* **About Us Footer:** High-contrast `#353531` footer detailing the engineering team, hackathon context, and quick navigation links.

---

## ⚙️ Backend API & Edge Infrastructure

The backend runs entirely on **Cloudflare Workers** backed by **Cloudflare D1** (distributed SQLite), providing stateful user management, community messaging, and API routing with zero server maintenance.

### API Endpoints

| Method | Endpoint | Auth Required | Description |
| :--- | :--- | :---: | :--- |
| `GET` | `/` | No | Health check returning API metadata and operational status. |
| `POST` | `/register` | No | Registers a new user (`username`, `email`, `password`), hashes credentials, and issues a 7-day `HttpOnly` session cookie. |
| `POST` | `/login` | No | Authenticates credentials against PBKDF2-SHA256 hashes and creates a session token. |
| `POST` | `/logout` | Cookie | Invalidates the active session token in D1 and clears the client cookie. |
| `GET` | `/me` | Cookie | Returns the currently authenticated user profile and verification status. |
| `POST` | `/verification/send` | Cookie | Generates a 64-character hex verification token and dispatches an email via EmailJS (rate-limited to 3 per 24h). |
| `GET` | `/verify?token=...` | No | Validates the single-use verification token and marks the user's account as verified. |
| `GET` | `/msg` | No | Retrieves the latest 100 global community messages in chronological order. |
| `POST` | `/msg` | Verified Cookie | Posts a new message (1–1,000 characters) to the global feed (requires verified email). |

### Database Schema (Cloudflare D1)

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT UNIQUE NOT NULL,
    email TEXT UNIQUE NOT NULL,
    password_hash TEXT NOT NULL,
    password_salt TEXT NOT NULL,
    verified INTEGER DEFAULT 0,
    verification_token TEXT,
    verification_expires_at INTEGER,
    created_at INTEGER NOT NULL
);

CREATE TABLE sessions (
    token_hash TEXT PRIMARY KEY,
    user_id INTEGER NOT NULL,
    expires_at INTEGER NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE TABLE verification_email_limits (
    user_id INTEGER PRIMARY KEY,
    window_started_at INTEGER NOT NULL,
    send_count INTEGER NOT NULL
);

CREATE TABLE messages (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    sender_id INTEGER NOT NULL,
    receiver TEXT NOT NULL,
    content TEXT NOT NULL,
    created_at INTEGER NOT NULL,
    FOREIGN KEY (sender_id) REFERENCES users(id)
);
```

---

## 🔒 Security & Authentication Model

Even in a hackathon environment, EquinoxAI implements production-grade web security practices natively via the Web Crypto API:

1. **Password Hashing (`PBKDF2`):** Passwords (minimum 12 characters, max 1024 bytes) are salted with 16 cryptographically random bytes and derived using **100,000 iterations of PBKDF2-SHA256**.
2. **Hashed Session Storage:** Raw 256-bit session tokens are never stored in the database; only their **SHA-256 digests** are persisted in D1, mitigating database leak risks.
3. **Hardened Cookies:** Session cookies use `HttpOnly`, `Path=/`, `Max-Age=604800`, and dynamically toggle `SameSite=None; Secure` on HTTPS/loopback origins.
4. **Strict CORS Policy:** Cross-origin requests with credentials are strictly checked against `env.CORS_ORIGINS` (or loopback addresses during local development).
5. **SQL Abuse & Rate Limiting:** All D1 queries use parameterized `.bind()` statements, and email verification dispatches are atomically throttled via `ON CONFLICT` upsert counters in SQLite.

---

## 🛠️ Getting Started & Local Development

### Prerequisites
* [Node.js](https://nodejs.org/) (v18+)
* [Wrangler CLI](https://developers.cloudflare.com/workers/wrangler/) (`npm install -g wrangler`)

### Installation & Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/adamcodetime/VERITAS_HACKATHON.git
   cd VERITAS_HACKATHON
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Configure Environment Variables (`.dev.vars`):**
   Create a `.dev.vars` file in the worker root for local testing:
   ```env
   CORS_ORIGINS=http://localhost:3000,http://localhost:5173
   RESEND_API_KEY=your_emailjs_user_id
   EMAILJS_PRIVATE_KEY=your_emailjs_private_key
   EMAILJS_SERVICE_ID=your_service_id
   EMAILJS_TEMPLATE_ID=your_template_id
   ```

4. **Initialize Local D1 Database & Start Dev Server:**
   ```bash
   npx wrangler d1 execute equinox-db --local --file=./schema.sql
   npm run dev
   ```
   The Worker API will be live at `http://localhost:8787/`.

5. **Deploy to Cloudflare Edge:**
   ```bash
   npm run deploy
   ```

---

## 📄 Engineering Write-Up

Interested in *why* we chose linear regression over deep neural networks, how we designed the UI psychology, and the architectural trade-offs behind our Cloudflare Worker implementation? 

👉 **Read our full technical write-up in [`WRITEUP.md`]().**