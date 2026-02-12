---
scope:
  domain: ["ssrf"]
priority: 95
applies_to:
  - "services making outbound HTTP/HTTPS requests"
  - "URL, webhook, callback, proxy features"
---

# SSRF Security

## Overview

SSRF lets an attacker make the server request attacker-controlled URLs, leading to internal network access, metadata theft, or privilege escalation. Applies to any code that performs outbound requests.

## Entry points

- URL parameters, webhook/callback URLs
- File download via URL, proxy/fetch/scraper features
- LLM tool/agent URL fetch

## Secure defaults

- Never request user-supplied URLs directly.
- Block access to internal networks by default.
- Minimize outbound requests.

## High-risk targets

- localhost, 127.0.0.1
- 169.254.169.254 (cloud metadata)
- 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
- file:// or Unix sockets

## Defenses

- **Input:** URL allowlist, restrict scheme (e.g. https only), block raw IP.
- **Network:** Egress firewall, block metadata endpoints, consider DNS rebinding.
- **Request:** Limit redirects, set timeout and response size limits.

## Do

- Re-validate host/IP after parsing URL.
- Check DNS result against allowlist/blocklist.
- Block internal IP ranges.
- Minimize outbound call surface.

## Don't

- Call `fetch(userInputUrl)` or equivalent without validation.
- Rely on string checks alone for URL filtering.
- Allow unlimited redirects.
- Assume "external API" implies safety.

## High-risk patterns

- Passing URL directly to HTTP client.
- Storing webhook URL in DB and reusing without re-validation.
- LLM agent able to fetch arbitrary URLs.
- Invoking curl/wget with user-controlled URL.

## Verification checklist

- [ ] No user-controlled URL used for outbound request without validation
- [ ] Internal IP and metadata access blocked
- [ ] Redirect handling limited
- [ ] Egress policy documented or enforced
