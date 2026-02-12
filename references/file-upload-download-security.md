---
scope:
  domain: ["file-upload", "file-download"]
priority: 90
---

# File Upload & Download Security

## Secure Defaults

- Validate file type on server (not just extension)
- Use dedicated upload directory outside webroot
- Remove execute permission from uploaded files

## Common Attacks

- Web shell upload
- Path traversal
- MIME type bypass

## Do

- Use random/non-guessable filenames for stored uploads
- Set Content-Disposition header for downloads
- Validate file content, not just extension
- Limit file size

## Don't

- Trust client-provided filename directly
- Store uploads in publicly accessible directories
- Allow path characters in filenames

## High-Risk Patterns

- `save(userFilename)` without sanitization
- Path concatenation with user input
- Missing content-type validation

## Verification Checklist

- [ ] Server-side file type validation
- [ ] Filenames sanitized before storage
- [ ] Upload directory isolated from webroot
- [ ] Content-Disposition set for downloads
