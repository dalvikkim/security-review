---
scope:
  language: ["java"]
priority: 80
applies_to:
  - "web services"
  - "enterprise apps"
  - "libraries processing untrusted data"
---

# Java General Security

## Core risks

- Injection: SQL, LDAP, EL, OGNL (framework-dependent)
- Unsafe deserialization (Java serialization)
- XXE (XML parser defaults)
- SSRF (HTTP client), open redirect
- Missing auth/authz and IDOR
- Secrets in logs/exceptions

## Secure defaults

- **DB:** PreparedStatement or ORM with parameter binding.
- **XML:** Disable DTD/external entities by default (XXE).
- **Serialization:** Avoid Java native serialization for untrusted data.
- Set secure defaults explicitly in code/config.

## Do

- Apply input validation via DTO/Validator consistently.
- Use standard crypto with safe modes (e.g. AEAD).
- **Paths:** Normalize and restrict to under a root directory.
- **Auth:** Check permissions at controller and service/domain level.

## Don't

- Build SQL/LDAP with string concatenation.
- Deserialize untrusted data with ObjectInputStream.
- Use XML parser defaults (external entities enabled).
- Expose stack traces to users.

## High-risk patterns

- Statement.execute("..." + user)
- new ObjectInputStream(socket.getInputStream()).readObject()
- DocumentBuilderFactory/SAXParserFactory without security options
- Relying on UI/front-end for access control only

## Verification checklist

- [ ] Binding used for SQL/LDAP etc.
- [ ] Java serialization/XXE defenses configured
- [ ] Secrets masked in exceptions/logs
- [ ] Auth/authz enforced on server
- [ ] Dependency scanning and version management in place
