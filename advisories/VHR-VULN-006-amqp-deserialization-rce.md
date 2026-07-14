# vhr（微人事）mailserver 反序列化 RCE（VHR-VULN-006）

| 字段 | 内容 |
|------|------|
| 厂商 | lenve |
| 产品 | vhr（微人事） |
| 版本 | 1.0-SNAPSHOT |
| 类型 | 不可信数据反序列化 |
| CWE | CWE-502 |
| 入口 | RabbitMQ Management / AMQP（默认 `guest`/`guest`，`15672` / `5673`） |

## 简述

`mailserver` 消费 `javaboy.mail.queue` 时用的是 Spring 默认的 Java 原生序列化。  
Management 或 AMQP 随便谁能连上，就可以投一条 `application/x-java-serialized-object`，在 **mailserver 所在机器** 上出 RCE。  
注意：不是 RabbitMQ 容器自己在反序列化，Broker 只管转发。

代码上两处：

- `RabbitConfig` 没有换成 JSON 转换器  
- `MailReceiver` 监听队列后走 `SerializationUtils.deserialize`

## 复现（Yakit）

完整包：[`../poc/VHR-VULN-006/YAKIT-PoC.md`](../poc/VHR-VULN-006/YAKIT-PoC.md)  
前提：mailserver 在跑，队列 `consumers=1`；classpath 里得有 gadget（验证时加了 `commons-beanutils`）。

### 1. Management 弱口令

```http
GET /api/overview HTTP/1.1
Host: 127.0.0.1:15672
Authorization: Basic Z3Vlc3Q6Z3Vlc3Q=
Accept: application/json
Connection: close


```

`guest:guest` 直接 200。

![overview](./screenshots/VHR-VULN-006-01-overview.png)

### 2. 能列到业务队列

```http
GET /api/queues/%2F/javaboy.mail.queue HTTP/1.1
Host: 127.0.0.1:15672
Authorization: Basic Z3Vlc3Q6Z3Vlc3Q=
Accept: application/json
Connection: close


```

![queue](./screenshots/VHR-VULN-006-02-queue-detail.png)

### 3. 投 payload，宿主机落文件

往 `javaboy.mail.queue` 发一条 Java Chains 序列化对象（本地 PoC 是 `touch /tmp/vhr_deser_poc_ok`）。请求体太长，见 Yakit 文档。

Management 回 `{"routed":true}`。在 **mailserver 宿主机**（别进 rabbitmq 容器）：

```text
ls -la /tmp/vhr_deser_poc_ok
```

![publish](./screenshots/VHR-VULN-006-03-publish-routed.png)

![touch](./screenshots/VHR-VULN-006-04-touch-file.png)

## 影响

MQ 网段可达、又开着默认口令时，等于给 mailserver 进程开了一个远程执行入口。

## 修法

消息改用 `Jackson2JsonMessageConverter`（或同类），禁止原生 Java 序列化。  
关掉 guest 远程登、换强口令，Management / AMQP 别裸奔到公网。消费端 classpath 尽量少带 gadget。

## 参考

- Yakit：[`../poc/VHR-VULN-006/YAKIT-PoC.md`](../poc/VHR-VULN-006/YAKIT-PoC.md)  
- 上游：https://github.com/lenve/vhr
