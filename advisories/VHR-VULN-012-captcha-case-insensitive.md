# vhr（微人事） — Login Captcha Case-Insensitive (VHR-VULN-012)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Improper Authentication |
| **CWE** | CWE-287 |
| **Authentication** | None |

## Summary

4-char captcha compared with case-insensitive logic, halving brute-force entropy.

## Root cause

`LoginFilter.java:70` — case-insensitive captcha comparison.

## Proof of Concept

```http
POST /doLogin HTTP/1.1
Host: TARGET
Content-Type: application/json;charset=UTF-8
Cookie: JSESSIONID=SESSION
Connection: close

{"username":"libai","password":"123","code":"ABC1"}
```

**Expected:** Login success when OCR shows lowercase `abc1`

## Impact

Easier automated login brute force.

## Remediation

Use case-sensitive comparison; increase captcha length/complexity.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-012
- PoC: `POC_VERIFICATION_REPORT.md`
