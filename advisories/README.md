# vhr（微人事） Security Advisories

Public advisory / exploit write-ups for VulDB CVE submissions (VHR-VULN-* series).

## 漏洞列表

| ID | 文件 | 严重度 | 需登录 |
|----|------|--------|--------|
| VHR-VULN-001 | [VHR-VULN-001-mass-assignment-privesc.md](./VHR-VULN-001-mass-assignment-privesc.md) | Critical | Low-privilege user (libai/123) |
| VHR-VULN-003 | [VHR-VULN-003-csrf-disabled.md](./VHR-VULN-003-csrf-disabled.md) | High | Any authenticated session |
| VHR-VULN-004 | [VHR-VULN-004-password-hash-disclosure.md](./VHR-VULN-004-password-hash-disclosure.md) | High | Any logged-in user |
| VHR-VULN-005 | [VHR-VULN-005-unrestricted-upload.md](./VHR-VULN-005-unrestricted-upload.md) | High | Any logged-in user |
| VHR-VULN-006 | [VHR-VULN-006-amqp-deserialization-rce.md](./VHR-VULN-006-amqp-deserialization-rce.md) | Critical | Network access to RabbitMQ AMQP (5673) |
| VHR-VULN-007 | [VHR-VULN-007-role-login-url-bypass.md](./VHR-VULN-007-role-login-url-bypass.md) | High | Any logged-in user |
| VHR-VULN-008 | [VHR-VULN-008-default-credentials.md](./VHR-VULN-008-default-credentials.md) | Critical | None (login endpoint) |
| VHR-VULN-009 | [VHR-VULN-009-password-change-idor.md](./VHR-VULN-009-password-change-idor.md) | High | Any logged-in user |
| VHR-VULN-010 | [VHR-VULN-010-avatar-upload-idor.md](./VHR-VULN-010-avatar-upload-idor.md) | Medium | Any logged-in user |
| VHR-VULN-012 | [VHR-VULN-012-captcha-case-insensitive.md](./VHR-VULN-012-captcha-case-insensitive.md) | Medium | None |

## Push to GitHub

```bash
cd vhr-master
git add SECURITY_AUDIT_REPORT.md POC_VERIFICATION_REPORT.md advisories/
git commit -m "Add security advisories"
git push
```

## Auto-fill poc_verify queue

```bash
cd ../poc_verify
python sync_*_queue.py
python vuldb_submit.py prepare
```
