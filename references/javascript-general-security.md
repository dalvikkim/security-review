---
scope:
  language: ["javascript"]
priority: 80
applies_to:
  - "node backends"
  - "browser frontends"
  - "shared libraries"
---

# JavaScript General Security

## Core risks

- XSS (DOM manipulation, innerHTML)
- Prototype pollution
- Injection (SQL/NoSQL/command), mainly on Node
- SSRF (server-side fetch/axios)
- Dependency/supply-chain (npm)

## Secure defaults

- **DOM:** Avoid innerHTML and dangerouslySetInnerHTML with untrusted data.
- **Node:** Input validation (schema), parameterized queries; minimize subprocess use.
- **Packages:** Use lockfile, pin versions, update regularly, run vulnerability scans.

## Do

- Validate and normalize user input.
- **Cookies/session:** Prefer httpOnly, sameSite set by server.
- **Outbound:** Allowlist, timeout, redirect limits, response size limits.
- **JSON/merge:** Guard against prototype pollution in deep merge (filter __proto__, constructor).

## Don't

- Pass user input to eval or new Function.
- Use child_process.exec(userInput).
- Pass user-controlled objects directly into NoSQL queries.
- Use vulnerable or unpinned packages.

## High-risk patterns

- element.innerHTML = userInput
- Object.assign({}, userObj) or deep merge without key filtering (__proto__, constructor)
- fetch(userUrl) on server (SSRF)
- exec("cmd " + user)

## Verification checklist

- [ ] XSS-prone APIs (e.g. innerHTML) reviewed
- [ ] Prototype pollution guarded (key filtering)
- [ ] SSRF controls (allowlist/timeout/redirect)
- [ ] Dependencies locked, pinned, scanned
