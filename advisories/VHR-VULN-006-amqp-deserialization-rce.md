# vhr（微人事） — Spring AMQP Java Deserialization RCE (VHR-VULN-006)

| Field | Value |
|-------|-------|
| **Vendor** | lenve |
| **Product** | vhr（微人事） |
| **Version** | 1.0-SNAPSHOT |
| **Type** | Deserialization of Untrusted Data |
| **CWE** | CWE-502 |
| **Authentication** | Network access to RabbitMQ AMQP (5673) |

## Summary

mailserver consumes `javaboy.mail.queue` with default Java serialization. Publishing malicious serialized objects causes RCE on mailserver JVM host.

## Root cause

`RabbitConfig` uses default `SimpleMessageConverter`; `MailReceiver` deserializes untrusted bytes.

## Proof of Concept

```
# Publish Java Chains payload to queue javaboy.mail.queue (port 5673)
python3 vhr_rabbitmq_publish.py --host TARGET --port 5673 --file vhr_payload_touch.b64
# Verify on mailserver host: ls /tmp/vhr_deser_poc_ok
```

**Expected:** File `/tmp/vhr_deser_poc_ok` created on mailserver host (not RabbitMQ container)

## Impact

Remote code execution on mailserver application server.

## Remediation

Use JSON message converter; authenticate RabbitMQ; restrict network access.

## References

- Audit: `SECURITY_AUDIT_REPORT.md` §VHR-VULN-006
- PoC: `POC_VERIFICATION_REPORT.md`
