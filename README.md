# TreasuryFlow

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![Security](https://img.shields.io/badge/Security-Reviewed-green.svg)](SECURITY.md)
[![Status](https://img.shields.io/badge/Status-Prototype%20%2F%20Educational-yellow.svg)]()

**TreasuryFlow** is a comprehensive, fully interactive front-end prototype for an enterprise-grade treasury and cash management platform. It is designed to give global finance teams a single source of truth by demonstrating how a centralized overlay can connect disparate ERP data sources, eliminate silos, and empower strategic financial decision-making.

> **Disclaimer:** This project is for educational and portfolio purposes only. It uses entirely mocked data and does not connect to any real financial systems or databases. It is not production-ready. See [SECURITY.md](SECURITY.md) for full details.

---

## Features

### CFO-Ready Dashboard
- Multi-currency global cash position across simulated international bank accounts
- Interactive KPI cards: Available Cash, AP Outstanding, AR Outstanding, Working Capital Ratio, DSO, DPO, and Cash Conversion Cycle
- 30-day projected cash flow chart, AP status doughnut, and AR aging bar chart
- Click any KPI card for a trend analysis modal with contextual recommendations

### AP & AR Management
- Full accounts payable and receivable tables with status badges
- Invoice drill-down modals showing vendor/customer details, related PO/GR/SO numbers, and approval history
- AI-powered follow-up email drafting via the Google Gemini API

### Treasury Operations
- Live (delayed) market data ticker tape powered by TradingView (EUR/USD, DAX, S&P 500, Gold)
- Consolidated multi-currency cash position table with real-time FX conversion
- FX hedging & derivatives proof-of-concept (Forward contracts and Options modeling)

### Financial Reporting & Analysis
- Dual-standard financial statements: IFRS and Local GAAP views side by side
- Editable income statement and balance sheet cells (yellow-highlighted) for scenario modeling
- Credit & Ratio Analysis tab: Current Ratio, Quick Ratio, D/E, Net Profit Margin, ROE, Times Interest Earned — with live recalculation and interpretations

### Interactive Process Flows
- Visual end-to-end workflows for Procure-to-Pay (P2P) and Order-to-Cash (O2C)
- Document linking: selecting an invoice or receivable highlights its current stage in the workflow

### Master Data Management
- Vendor and customer master data viewer across three simulated company codes (Germany, USA, UK)

### AI Treasury Assistant
- Context-aware chatbot on the Dashboard powered by the Google Gemini API
- Answers financial questions using live KPI data from the session

### Governance & Security
- Role-Based Access Control (RBAC) permissions matrix
- API connection status monitor
- Live in-memory session audit log (login, logout, invoice views)

---

## Security

This repository has undergone a full security and governance review. Key controls applied to the prototype:

| Control | Status |
|---------|--------|
| XSS prevention (`escapeHTML()` + DOM API) | Applied throughout |
| Content-Security-Policy meta tag | Configured |
| CDN dependency version pinning | Chart.js 4.4.3, Font Awesome 6.4.0 |
| API key placeholder with backend-proxy comments | Applied |
| Prototype pollution guard on login | Applied (`Object.prototype.hasOwnProperty`) |
| Login brute-force lockout (5 attempts / 30s) | Applied |
| Exchange rate API response validation | Applied (allowlist + numeric type check) |
| Gemini API null-safe response handling | Applied (optional chaining) |
| Sanitized console logging | Applied (no raw error objects) |
| TradingView widget CSP frame-src | Configured |
| Session state clearing on logout | Applied |
| Local SVG avatar (no third-party avatar service) | Applied |
| `.gitignore` for secrets and env files | Configured |

See [SECURITY.md](SECURITY.md) for a full list of known prototype limitations and the production architecture requirements before this application could handle real data.

---

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Structure | HTML5 |
| Logic | JavaScript ES6+ (vanilla, no framework) |
| Styling | [Tailwind CSS](https://tailwindcss.com/) (CDN) |
| Charts | [Chart.js 4.4.3](https://www.chartjs.org/) |
| Icons | [Font Awesome 6.4.0](https://fontawesome.com/) |
| Market Data | [TradingView Widgets](https://www.tradingview.com/widget/) |
| AI / LLM | [Google Gemini API](https://ai.google.dev/) |
| FX Rates | [ExchangeRate-API](https://www.exchangerate-api.com/) |

---

## Getting Started

This prototype is a single self-contained `index.html` file with no build step or local server required.

```bash
git clone https://github.com/arnav-ray/TreasuryFlow.git
cd TreasuryFlow
open index.html   # macOS
# or: start index.html  (Windows)
# or: xdg-open index.html  (Linux)
```

The application runs entirely in the browser. No internet connection is required for the core UI; the exchange rate ticker, TradingView widget, and AI features require internet access.

---

## Demo Accounts

> Credentials are intentionally simple for demo purposes. See [SECURITY.md](SECURITY.md) for why these are not production-safe.

| Username | Role | Permissions |
|----------|------|-------------|
| `admin` | CFO • Admin | Full access including role management |
| `finance` | Controller • Finance | View and approve invoices |
| `approver` | Manager • Approver | View only |

**Password for all accounts:** `password`

After 5 failed login attempts the Sign In button locks for 30 seconds.

---

## AI Features (Optional)

The Gemini AI features (Treasury Assistant chatbot and AP email drafting) require a valid Google Gemini API key. For security, **the API key must never be embedded in client-side code**. The current prototype has an empty placeholder. To enable AI features:

1. Set up a backend proxy endpoint (e.g., a Cloud Function or Edge Function)
2. Inject your API key server-side into the request
3. Point the `apiUrl` constants in `index.html` to your proxy endpoint

See [SECURITY.md](SECURITY.md) § 4.3 for the full production secret management architecture.

---

## Project Structure

```
TreasuryFlow/
├── index.html      # Entire application (HTML + CSS + JS)
├── LICENSE         # Apache 2.0
├── README.md       # This file
├── SECURITY.md     # Security policy, known limitations, production requirements
└── .gitignore      # Excludes .env, secrets, node_modules, OS artifacts
```

---

## Roadmap (Production Architecture)

To evolve from prototype to production the following would be required:

- **Backend API** (Node.js / Python / Go) with JWT/OAuth 2.0 authentication
- **Database** (PostgreSQL via Supabase) with Row Level Security policies
- **Secret Management** (AWS Secrets Manager / HashiCorp Vault) for all API keys
- **Real-time ERP connectors** (SAP, Oracle, NetSuite) via API gateway
- **Immutable server-side audit trail** with 7-year retention
- **CI/CD pipeline** with SAST, dependency scanning, and secret scanning
- **GDPR compliance** module (consent, DSAR, right-to-erasure)

---

## License

Copyright 2025 Arnav Amal Ray

Licensed under the **Apache License, Version 2.0**. See [LICENSE](LICENSE) for the full license text.

You may use, copy, modify, and distribute this work for any purpose, with or without modification, as long as you include the copyright notice and license.

---

## Author

**Arnav Amal Ray**
Email: [arnav@arnavray.ca](mailto:arnav@arnavray.ca)
GitHub: [@arnav-ray](https://github.com/arnav-ray)
