---
scope:
  language: ["c", "cpp"]
priority: 80
---

# C / C++ Security

## Core Risks

- Memory safety: buffer overflow, use-after-free, double free
- Integer overflow/underflow leading to memory corruption
- Format string vulnerabilities
- Path/file issues (TOCTOU, symlink, path traversal)
- Concurrency and race conditions

## Secure Defaults

- Use safe functions: snprintf, strlcpy/strlcat, length-bounded memcpy
- Manage strings/buffers with length + pointer
- Enable compiler hardening: stack protector, PIE, RELRO, NX, FORTIFY
- Use sanitizers (ASan, UBSan) in tests

## Do

- Validate length, range, and format of external input
- Use overflow-safe length/offset calculations
- Document ownership for dynamic memory; NULL after free
- Use O_NOFOLLOW and safe temp-file APIs

## Don't

- Use gets, strcpy, strcat, sprintf
- Use user input as format string
- Rely on int for length calculations
- Ignore return values

## High-Risk Patterns

- `memcpy(dst, src, len)` with user-controlled len
- `malloc(n * sizeof(T))` with possible overflow
- Ignoring snprintf return value
- Partial init/free in error paths

## Verification Checklist

- [ ] Bounds checked for all buffer access
- [ ] Length-limited string functions used
- [ ] No user input in format strings
- [ ] Integer overflow-safe calculations
- [ ] Compiler hardening and sanitizers enabled
