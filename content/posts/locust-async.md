2020-11-19 · 技术

---

很久之前写的项目，为 [Locust](https://locust.io/) 压测框架扩展了 WebSocket 和 SSH 两种协议的异步压测支持。

## 背景

Locust 本身只支持 HTTP/HTTPS 协议（通过 `HttpUser`），但在实际工作中经常会遇到需要压测 WebSocket 服务或 SSH 服务的情况。这个插件通过继承 Locust 的 `User` 基类，实现了两种自定义用户类型。

## WebSocketUser

用于压测 WebSocket 服务，支持 WSS 协议。

核心实现：

```python
class WebSocketUser(HttpUser):
    abstract = True

    def on_open(self, ws):
        body = json.dumps({'data': "id\\r\\n"})
        ws.send(body)
        self.send_time = time.monotonic()
        self.environment.events.request_success.fire(
            request_type="WSS",
            name="sent",
            response_time=None,
            response_length=len(body)
        )

    def on_message(self, ws, message):
        if self.expect_recv_msg in message:
            response_time = int(
                round(time.monotonic() - self.send_time, 3) * 10000
            )
            self.environment.events.request_success.fire(
                request_type="WSR",
                name="recieved result msg",
                response_time=response_time,
                response_length=len(message)
            )
```

关键点：

- 用 `WebSocketApp` 建立连接，用 `gevent.spawn` 异步运行事件循环
- 连接成功后发送 `id` 指令并计时，收到预期响应后记录耗时
- 通过 `request_success.fire` 把结果注入 Locust 的统计系统
- 超时控制通过 `gevent.spawn(...).join(timeout=20)` 实现

## SSHUser

用于压测 SSH 服务，通过 subprocess 调用系统 SSH 客户端。

```python
class SSHUser(User):
    abstract = True

    def test(self, user='', scmd='', expect='', timeout=20):
        cmd = f"ssh {user}@{self.host} '{scmd}'"
        with subprocess.Popen(cmd, shell=True, ...) as p:
            if p.stdout.read() == expect:
                self.environment.events.request_success.fire(
                    request_type="SSHR",
                    response_time=...
                )
```

设计思路：

- 复用系统 SSH 客户端，不引入第三方 SSH 库
- 发送命令后对比预期输出来判定成功/失败
- 支持 20 秒超时自动中断
- 异常的完整度量和上报

## 用法示例

```python
from locust_users import WebSocketUser, SSHUser

class MyWebSocketTest(WebSocketUser):
    host = "wss://example.com/ws"

    def test(self):
        self.connectApp(
            ws_url=self.host,
            expect_recv_msg="expected_response"
        )

class MySSHTest(SSHUser):
    host = "192.168.1.1"

    def test(self):
        self.test(
            user="admin",
            scmd="uptime",
            expect=b"load average"
        )
```

## 总结

虽然代码量不大，但解决了一个实际问题：在 Locust 生态中缺少对非 HTTP 协议的支持。这个插件在保持 Locust 统计框架不变的前提下，以最小的侵入性扩展了协议支持。
