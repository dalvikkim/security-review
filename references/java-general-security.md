---
scope:
  language: ["java", "kotlin"]
priority: 80
---

# Java / Kotlin Security

## Core Risks

- Injection: SQL, LDAP, EL, OGNL
- Unsafe deserialization (Java serialization)
- XXE (XML parser defaults)
- SSRF, open redirect
- Missing auth/authz and IDOR

## Secure Defaults

- Use PreparedStatement or ORM with parameter binding
- Disable DTD/external entities in XML parsers
- Avoid Java native serialization for untrusted data
- Set secure defaults explicitly in code/config

## Do

- Apply input validation via DTO/Validator
- Use standard crypto with safe modes (AEAD)
- Normalize paths and restrict to root directory
- Check permissions at controller and service level

## Don't

- Build SQL/LDAP with string concatenation
- Deserialize untrusted data with ObjectInputStream
- Use XML parser defaults (external entities enabled)
- Expose stack traces to users

## High-Risk Patterns

- `Statement.execute("..." + user)`
- `new ObjectInputStream(input).readObject()`
- DocumentBuilderFactory without security options
- Front-end-only access control

## Verification Checklist

- [ ] Parameter binding used for SQL/LDAP
- [ ] Java serialization/XXE defenses configured
- [ ] Secrets masked in logs
- [ ] Auth/authz enforced server-side
- [ ] Dependency scanning in place
