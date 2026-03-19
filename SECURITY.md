# TreasuryFlow — Security & Governance Policy

> **This document is the authoritative security reference for the TreasuryFlow repository.**
> All contributors, reviewers, and deployers must read this before working with this codebase.

---

## 1. Prototype Status

TreasuryFlow is an **educational proof-of-concept** only. It:
- Uses entirely mocked financial data
- Has no database or backend server
- Is NOT suitable for any real financial data or production deployment

**Do not enter real credentials, financial data, or API keys into this application.**

---

## 2. Known Security Limitations (by design for the prototype)

| # | Finding | Severity | Status |
|---|---------|----------|--------|
| C1 | Client-side only authentication — no backend validation | Critical | Prototype limitation |
| C2 | RBAC permissions enforced by UI only (trivially bypassable via DevTools) | Critical | Prototype limitation |
| C3 | Plaintext password comparison (no hashing) | Critical | Prototype limitation |
| H1 | No persistent audit trail — in-memory log only | High | Prototype limitation |
| H2 | No multi-tenancy data isolation at the data layer | High | Prototype limitation |
| M1 | Tailwind CDN cannot be SRI-hashed (dynamic CSS generation) | Medium | Requires build pipeline |
| M2 | TradingView embed loaded without SRI integrity hash | Medium | Requires known hash |
| M3 | No server-side rate limiting on AI API calls | Medium | Client-side debounce only |

---

## 3. Security Controls Implemented

The following controls have been applied in this prototype:

| Control | Implementation |
|---------|---------------|
| XSS prevention | `escapeHTML()` helper + DOM API (`textContent`) for user input |
| CSP meta tag | Baseline Content-Security-Policy in `<head>` |
| X-Content-Type-Options | `nosniff` meta tag |
| CDN dependency pinning | Chart.js pinned to `4.4.3`; Font Awesome pinned to `6.4.0` |
| API key placeholder comments | `SECURITY:` comments marking all API key locations |
| Null-safe API responses | Optional chaining (`?.`) on all Gemini API property accesses |
| Sanitized error logging | Raw error objects not logged; no stack traces in console |
| Login credential masking | Credentials removed from login UI (see README for demo access) |
| PII anonymization | User profile names replaced with clearly fictional placeholders |
| Client-side audit log | Login, logout, and invoice-view events recorded in-memory |
| Logout state clearing | `currentUser` object wiped; charts destroyed on sign-out |
| Avatar privacy | User initials rendered as local inline SVG (no third-party service) |
| AI chat rate limiting | 3-second client-side cooldown on AI send button |
| `.gitignore` | Excludes `.env` files, secrets, credentials, and OS artifacts |

---

## 4. Production Architecture Requirements

**Before handling any real data, a complete architectural redesign is required:**

### 4.1 Authentication
- Replace client-side auth with **OAuth 2.0 / SAML 2.0** via a trusted IdP (Okta, Auth0, Azure AD)
- Enforce **MFA** for all users
- Use **short-lived JWTs** with refresh token rotation
- Implement **session timeout** (15 min inactivity)
- Hash all passwords with **bcrypt** (min cost factor 12) or **Argon2id**

### 4.2 Authorization
- Move all permission enforcement to the **backend / database layer**
- Use **Row Level Security (RLS)** in Supabase/PostgreSQL for data isolation
- Implement **least-privilege access** — users see only their company's data
- Log all authorization failures

### 4.3 API Security
- **Never embed API keys in frontend code** — proxy all third-party API calls through a backend endpoint
- Use **environment variables** for all secrets (injected at deploy time)
- Integrate a **secrets manager** (AWS Secrets Manager, HashiCorp Vault, or GCP Secret Manager)
- Implement **server-side rate limiting** per user and per IP

### 4.4 Data Security
- Encrypt all data **at rest** (AES-256) and **in transit** (TLS 1.2+)
- Enforce **HTTPS** with HSTS (`max-age=31536000; includeSubDomains; preload`)
- Add **X-Frame-Options: DENY** to prevent clickjacking
- Implement proper **CORS** policy restricting allowed origins
- Mask/redact sensitive values (account numbers, balances) in all logs

### 4.5 Audit Logging
- Implement an **immutable server-side audit trail** with:
  - User ID, timestamp (UTC), action type, resource ID, before/after state
  - IP address and User-Agent
  - Success/failure status
- Store logs in a **separate, append-only** system (separate DB schema or WORM storage)
- Retain logs per your compliance policy (e.g., **7 years** for SOC2 / financial regulations)
- Alert on anomalous access patterns

### 4.6 Dependency Security
- Use a **build pipeline** (Vite, webpack) to bundle and hash all assets
- Add **SRI integrity hashes** to all external CDN resources
- Run **`npm audit`** (or equivalent) in CI
- Enable **Dependabot** or **Snyk** for automated dependency scanning
- Pin all dependency versions

### 4.7 CI/CD Security
- Add **GitHub Actions** workflow with:
  - SAST scanning (Semgrep, SonarQube)
  - Secret scanning (TruffleHog, GitHub Secret Scanning)
  - Dependency vulnerability scanning
  - Build artifact signing
- Enforce **branch protection rules** (required reviews, status checks)
- Never allow direct pushes to `main`

---

## 5. Compliance Considerations

| Standard | Gap | Production Requirement |
|----------|-----|----------------------|
| **GDPR** | No consent management, no DSAR workflow, no right-to-erasure | Implement privacy notice, consent flows, and data deletion APIs |
| **SOC 2 Type II** | No monitoring, change control, or incident response | Implement SIEM, change management, and IR playbooks |
| **PCI-DSS** | Not applicable (no card data handled) | Scope carve-out if payment card data is ever added |
| **ISO 27001** | No formal ISMS | Requires full information security management system |

---

## 6. Vulnerability Disclosure

If you discover a security issue in this prototype:

1. **Do not** open a public GitHub issue
2. Email the maintainer directly: arnav@arnavray.ca
3. Include a description of the vulnerability and reproduction steps
4. Allow 7 days for acknowledgement before any public disclosure

---

## 7. Security Contacts

| Role | Contact |
|------|---------|
| Repository Owner | Arnav Amal Ray — arnav@arnavray.ca |

---

*Last reviewed: 2026-03-19 | Branch: `claude/security-governance-review-ER0Fp`*
