---
scope:
  language: ["javascript", "typescript"]
priority: 80
---

# JavaScript / TypeScript Security

## Core Risks

- XSS (DOM manipulation, innerHTML)
- Prototype pollution
- Injection (SQL/NoSQL/command) on Node.js
- SSRF (server-side fetch/axios)
- Dependency/supply-chain (npm)

## Secure Defaults

- **DOM:** Avoid innerHTML with untrusted data
- **Node:** Input validation (schema), parameterized queries
- **Packages:** Use lockfile, pin versions, run vulnerability scans

## Do

- Validate and normalize user input
- Use httpOnly, sameSite cookies
- Guard against prototype pollution (filter `__proto__`, `constructor`)
- Set timeout, redirect limits on outbound requests

## Don't

- Pass user input to `eval` or `new Function`
- Use `child_process.exec(userInput)`
- Pass user objects directly into NoSQL queries
- Use unpinned or vulnerable packages

## High-Risk Patterns

- `element.innerHTML = userInput`
- `Object.assign({}, userObj)` without key filtering
- `fetch(userUrl)` on server without validation
- `exec("cmd " + user)`

## Verification Checklist

- [ ] XSS-prone APIs reviewed
- [ ] Prototype pollution guarded
- [ ] SSRF controls in place
- [ ] Dependencies locked and scanned
