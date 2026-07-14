# vhr（微人事） — Global CSRF Protection Disabled (VHR-VULN-003)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Cross-Site Request Forgery |
| **CWE** | CWE-352 |
| **Authentication** | Any authenticated session |

## Summary

Spring Security CSRF is globally disabled; cross-origin state-changing requests succeed.

## Root cause

`SecurityConfig.java:130` — `csrf().disable()`.

## Proof of Concept

```http
PUT /hr/info HTTP/1.1
Host: TARGET
Content-Type: application/json
Origin: http://evil.example
Cookie: JSESSIONID=SESSION_LIBAI
Connection: close

{"id":5,"name":"李白","enabled":true,"username":"libai"}
```

**Expected:** {"status":200,"msg":"更新成功!"} with foreign Origin header

## Impact

Forced profile/password changes for victims visiting attacker pages.

## Remediation

Enable CSRF tokens for session-authenticated JSON endpoints.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-003
- PoC: `POC_VERIFICATION_REPORT.md`
