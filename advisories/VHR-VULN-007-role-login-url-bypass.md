# vhr（微人事） — ROLE_LOGIN Fallback on Unregistered URLs (VHR-VULN-007)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Missing Authorization |
| **CWE** | CWE-862 |
| **Authentication** | Any logged-in user |

## Summary

URLs not registered in `menu` table only require `ROLE_LOGIN`, not admin roles.

## Root cause

`CustomFilterInvocationSecurityMetadataSource.java:47` — unmatched URLs get `ROLE_LOGIN`.

## Proof of Concept

```http
GET /chat/hrs HTTP/1.1
Host: TARGET
Cookie: JSESSIONID=SESSION_LIBAI
Accept: application/json
Connection: close
```

**Expected:** HTTP 200 with HR user list (should require admin for sensitive data)

## Impact

Access to endpoints omitted from menu RBAC table.

## Remediation

Default-deny unmapped URLs; register all controllers in menu or use `@PreAuthorize`.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-007
- PoC: `POC_VERIFICATION_REPORT.md`
