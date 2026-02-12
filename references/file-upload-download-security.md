---
scope:
  domain: ["file-upload", "file-download"]
priority: 90
---

# File Upload & Download Security

## Secure defaults

- Validate file type on the server.
- Use a dedicated upload directory (outside webroot when possible).
- Remove execute permission from uploaded files.

## Common attacks

- Web shell upload
- Path traversal
- MIME type bypass

## Do

- Use random or non-guessable filenames for stored uploads.
- Set Content-Disposition for downloads (avoid script execution).
