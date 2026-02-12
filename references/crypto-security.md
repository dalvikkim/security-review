---
scope:
  domain: ["cryptography", "crypto", "encryption"]
priority: 90
applies_to:
  - "Password storage and verification"
  - "Data encryption at rest and in transit"
  - "Token generation and validation"
  - "Digital signatures"
---

# Cryptographic Security

## Overview

Cryptographic misuse leads to data exposure, authentication bypass, and integrity violations. Most vulnerabilities stem from using deprecated algorithms, improper key management, or incorrect implementation.

## Core Risks

- Weak or deprecated algorithms (MD5, SHA1, DES, RC4)
- Hardcoded keys and secrets
- Insufficient key length
- Missing or improper IV/nonce handling
- ECB mode usage
- Custom cryptographic implementations
- Insecure random number generation

## Secure Defaults

- Use well-tested cryptographic libraries (not custom implementations)
- AES-256-GCM for symmetric encryption
- RSA-2048+ or ECC P-256+ for asymmetric encryption
- Argon2id, bcrypt, or scrypt for password hashing
- SHA-256 or SHA-3 for hashing
- CSPRNG for random number generation

## Password Storage

### Do
- Use adaptive hashing (Argon2id, bcrypt, scrypt)
- Configure appropriate cost factors
- Use unique salt per password (automatic with modern libs)

### Don't
- Store passwords in plaintext
- Use MD5, SHA1, or SHA256 alone for passwords
- Use the same salt for all passwords
- Implement custom password hashing

## Encryption

### Do
- Use authenticated encryption (GCM, ChaCha20-Poly1305)
- Generate unique IV/nonce for each encryption
- Use proper key derivation (PBKDF2, HKDF)
- Rotate encryption keys periodically

### Don't
- Use ECB mode
- Reuse IV/nonce with the same key
- Use encryption without authentication (CBC without HMAC)
- Hardcode encryption keys

## Key Management

### Do
- Store keys in secret managers (Vault, AWS KMS, etc.)
- Use separate keys for different purposes
- Implement key rotation
- Use envelope encryption for large data

### Don't
- Hardcode keys in source code
- Store keys alongside encrypted data
- Use weak key derivation
- Share keys across environments

## High-Risk Patterns

- `md5(password)` or `sha1(password)` for password storage
- `AES/ECB/PKCS5Padding`
- `new Random()` for security-sensitive values (use SecureRandom)
- `crypto.createCipher()` (deprecated, use createCipheriv)
- Hardcoded `key = "supersecret123"`
- `iv = new byte[16]` (zero IV)
- `Math.random()` for tokens or secrets

## Language-Specific Examples

### JavaScript/Node.js
```javascript
// VULNERABLE
const hash = crypto.createHash('md5').update(password).digest('hex');

// SECURE
const bcrypt = require('bcrypt');
const hash = await bcrypt.hash(password, 12);
```

### Python
```python
# VULNERABLE
import hashlib
hash = hashlib.md5(password.encode()).hexdigest()

# SECURE
from argon2 import PasswordHasher
ph = PasswordHasher()
hash = ph.hash(password)
```

### Java
```java
// VULNERABLE
MessageDigest md = MessageDigest.getInstance("MD5");
byte[] hash = md.digest(password.getBytes());

// SECURE
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();
String hash = encoder.encode(password);
```

### Go
```go
// VULNERABLE
hash := md5.Sum([]byte(password))

// SECURE
import "golang.org/x/crypto/bcrypt"
hash, _ := bcrypt.GenerateFromPassword([]byte(password), bcrypt.DefaultCost)
```

## Token Generation

### Do
- Use CSPRNG (crypto.randomBytes, SecureRandom, secrets module)
- Use sufficient length (32+ bytes for tokens)
- Use JWT with strong algorithms (RS256, ES256)

### Don't
- Use predictable values (timestamp, sequential IDs)
- Use Math.random() or similar non-cryptographic RNG
- Use JWT with "none" algorithm or HS256 with weak secrets

## Verification Checklist

- [ ] No MD5, SHA1, DES, RC4, or 3DES in use
- [ ] Passwords hashed with Argon2id, bcrypt, or scrypt
- [ ] AES-GCM or ChaCha20-Poly1305 for encryption
- [ ] Unique IV/nonce per encryption operation
- [ ] No ECB mode usage
- [ ] CSPRNG used for all security-sensitive random values
- [ ] No hardcoded keys or secrets
- [ ] Keys stored in secret managers
- [ ] Key rotation strategy documented
