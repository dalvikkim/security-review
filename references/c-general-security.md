---
scope:
  language: ["c"]
priority: 80
applies_to:
  - "native applications"
  - "embedded/firmware"
  - "libraries handling untrusted input"
---

# C General Security

## Core risks

- Memory safety: buffer overflow, use-after-free, double free
- Integer overflow/underflow → wrong length → memory corruption
- Format string (e.g. printf(user_input))
- Path/file (TOCTOU, symlink, path traversal)
- Concurrency and race conditions

## Secure defaults

- Prefer safe functions: snprintf, strlcpy/strlcat where available, length-bounded memcpy.
- Manage strings/buffers with length + pointer.
- Enable compiler/runtime hardening: stack protector, PIE, RELRO, NX, FORTIFY; use sanitizers (ASan, UBSan) in tests.
- Treat all input as untrusted.

## Do

- Validate length, range, and format of external input.
- Use overflow-safe length/offset calculations.
- Document ownership for dynamic memory; set pointer to NULL after free.
- For files: consider O_NOFOLLOW where applicable; use safe temp-file APIs.

## Don't

- Use gets, strcpy, strcat, sprintf.
- Use user input as format string (printf(user)).
- Rely on int for length calculations (platform/sign issues).
- Ignore return values.

## High-risk patterns

- memcpy(dst, src, len) with user-controlled len and no upper bound
- malloc(n * sizeof(T)) with possible overflow in n * sizeof(T)
- Ignoring snprintf return value (truncation)
- Partial init/free in error paths (leaks/double free)

## Verification checklist

- [ ] Bounds checked for all buffer access
- [ ] Length-limited string functions used throughout
- [ ] No user input in format strings
- [ ] Integer overflow-safe size calculations
- [ ] Compiler hardening and ASan in test pipeline
