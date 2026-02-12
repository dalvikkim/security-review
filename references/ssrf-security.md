---
scope:
  domain: ["ssrf"]
priority: 95
---

# SSRF Security

## Entry Points

- URL parameters, webhook/callback URLs
- File download via URL, proxy/fetch/scraper features
- LLM tool/agent URL fetch

## Secure Defaults

- Never request user-supplied URLs directly
- Block access to internal networks by default
- Minimize outbound requests

## High-Risk Targets

- localhost, 127.0.0.1
- 169.254.169.254 (cloud metadata)
- 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
- file:// or Unix sockets

## Defenses

- **Input:** URL allowlist, restrict scheme (https only), block raw IP
- **Network:** Egress firewall, block metadata endpoints
- **Request:** Limit redirects, set timeout and response size limits

## Do

- Re-validate host/IP after parsing URL
- Check DNS result against allowlist/blocklist
- Block internal IP ranges

## Don't

- Call `fetch(userInputUrl)` without validation
- Rely on string checks alone for URL filtering
- Allow unlimited redirects

## High-Risk Patterns

- Passing URL directly to HTTP client
- Storing webhook URL and reusing without re-validation
- LLM agent able to fetch arbitrary URLs
- Invoking curl/wget with user-controlled URL

## Verification Checklist

- [ ] No user-controlled URL used without validation
- [ ] Internal IP and metadata access blocked
- [ ] Redirect handling limited
- [ ] Egress policy documented or enforced
