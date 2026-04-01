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
| XSS prevention | `escapeHTML()` helper + DOM API (`textContent`) for user input throughout |
| CSP meta tag | Baseline Content-Security-Policy in `<head>`; production must use `vercel.json` headers |
| X-Content-Type-Options | `nosniff` meta tag |
| CSP `frame-src` | Restricts iframes to `https://*.tradingview.com` only |
| CSP `img-src` | Limited to `'self'` and `data:` URIs — no broad `https:` wildcard |
| CDN dependency pinning | Chart.js pinned to `4.4.3`; Font Awesome pinned to `6.4.0` |
| API key placeholders | Empty strings with `SECURITY:` comments directing to Vercel Edge/Serverless Function proxy |
| Null-safe API responses | Optional chaining (`?.`) on all Gemini API property accesses |
| Exchange rate validation | Allowlist of known currencies + numeric `> 0` type check before trusting API response |
| Sanitized error logging | Raw error objects not logged; no stack traces in console |
| Prototype pollution guard | `Object.prototype.hasOwnProperty.call()` prevents `__proto__`/`constructor` login bypass |
| Login brute-force lockout | 5 failed attempts locks the Sign In button for 30 seconds |
| Duplicate listener guard | `dataset.bound` marker prevents event listener stacking on tab re-renders |
| Client-side audit log | Login, logout, invoice-view, and governance-access events recorded in-memory |
| Logout state clearing | `currentUser` object wiped; chart instances destroyed; avatar `src` cleared |
| Avatar privacy | User initials rendered as local inline SVG `data:` URI (no third-party avatar service) |
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
- **Never embed API keys in frontend code** — proxy all third-party API calls through a Vercel Serverless or Edge Function (e.g., `/api/ai-chat`)
- Store secrets as **Vercel Environment Variables** (set in the Vercel Dashboard under Project → Settings → Environment Variables)
- For local development, use a `.env.local` file (already excluded by `.gitignore`)
- Implement **server-side rate limiting** per user and per IP in each `/api/*` route
- Consider Vercel's **Edge Middleware** for request-level rate limiting and authentication guards

### 4.4 Data Security
- Encrypt all data **at rest** (AES-256) and **in transit** (TLS 1.2+)
- Enforce **HTTPS** with HSTS — set via `vercel.json`:
  ```json
  {
    "headers": [
      {
        "source": "/(.*)",
        "headers": [
          { "key": "Strict-Transport-Security", "value": "max-age=31536000; includeSubDomains; preload" },
          { "key": "X-Frame-Options", "value": "DENY" },
          { "key": "X-Content-Type-Options", "value": "nosniff" },
          { "key": "Content-Security-Policy", "value": "default-src 'self'; ..." }
        ]
      }
    ]
  }
  ```
- Moving security headers to `vercel.json` is required — CSP `<meta>` tags cannot set `frame-ancestors` or `X-Frame-Options`, which only work as HTTP response headers
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
- Use **Vercel's GitHub integration** for automatic preview and production deployments
- Enable **Vercel Deployment Protection** (password or Vercel SSO) for preview URLs
- Add a **GitHub Actions** workflow for pre-merge checks:
  - SAST scanning (Semgrep, SonarQube)
  - Secret scanning (TruffleHog, GitHub Secret Scanning)
  - Dependency vulnerability scanning (`npm audit`)
  - Build artifact signing
- Enforce **branch protection rules** on `main` (required reviews, status checks, no direct pushes)

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

*Last reviewed: 2026-04-01 | Branch: `claude/security-governance-review-ER0Fp`*
