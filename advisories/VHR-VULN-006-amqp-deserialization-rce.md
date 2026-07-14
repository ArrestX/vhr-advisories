# vhr (微人事) — Spring AMQP Java Deserialization RCE (VHR-VULN-006)

| Field | Value |
|-------|-------|
| Vendor | lenve |
| Product | vhr |
| Version | 1.0-SNAPSHOT |
| Type | Deserialization of Untrusted Data |
| CWE | CWE-502 |
| Entry | RabbitMQ Management / AMQP (`guest`/`guest`, ports `15672` / `5673`) |

## Summary

`mailserver` consumes `javaboy.mail.queue` with Spring’s default Java serialization.  
Anyone who can talk to Management (or AMQP) can publish an `application/x-java-serialized-object` blob and get code execution on the **mailserver host**.

The broker itself does not deserialize Java — it only forwards bytes.

Code:

- `RabbitConfig` never switches to a JSON converter
- `MailReceiver` (`@RabbitListener`) ends up in `SerializationUtils.deserialize`

## PoC (Yakit)

Packets: [`../poc/VHR-VULN-006/YAKIT-PoC.md`](../poc/VHR-VULN-006/YAKIT-PoC.md)  
Need `consumers=1` on the queue. Lab build adds `commons-beanutils` so the CC/TemplatesImpl chain fires.

### 1. Default Management creds

```http
GET /api/overview HTTP/1.1
Host: 127.0.0.1:15672
Authorization: Basic Z3Vlc3Q6Z3Vlc3Q=
Accept: application/json
Connection: close


```

`guest:guest` → HTTP 200.

![overview](./screenshots/VHR-VULN-006-01-overview.png)

### 2. Queue visible

```http
GET /api/queues/%2F/javaboy.mail.queue HTTP/1.1
Host: 127.0.0.1:15672
Authorization: Basic Z3Vlc3Q6Z3Vlc3Q=
Accept: application/json
Connection: close


```

![queue](./screenshots/VHR-VULN-006-02-queue-detail.png)

### 3. Publish gadget → host file

Publish to `javaboy.mail.queue` (`content_type=application/x-java-serialized-object`). Lab payload runs `touch /tmp/vhr_deser_poc_ok`. Full body is in the Yakit doc.

Management replies `{"routed":true}`. On the **mailserver host** (not inside the RabbitMQ container):

```text
ls -la /tmp/vhr_deser_poc_ok
```

![publish](./screenshots/VHR-VULN-006-03-publish-routed.png)

![touch](./screenshots/VHR-VULN-006-04-touch-file.png)

## Impact

Network-reachable MQ with default creds + Java ser consumers = RCE on the mailserver JVM.

## Fix

Use `Jackson2JsonMessageConverter` (or similar). Drop native Java serialization.  
Disable remote `guest`, use real passwords, keep Management/AMQP off the public net. Trim gadget-rich libs from the consumer classpath where you can.

## References

- Yakit: [`../poc/VHR-VULN-006/YAKIT-PoC.md`](../poc/VHR-VULN-006/YAKIT-PoC.md)
- Upstream: https://github.com/lenve/vhr
