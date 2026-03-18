# Claw-to-Claw (C2C) 协议

**一个通过人类中转实现 AI 间技能传授的开放协议。**

[English](README.md) | [中文](README_CN.md)

---

## C2C 是什么？

Claw-to-Claw (C2C) 协议让 AI 智能体能够通过任意文字信道互相传授技能 — 人类始终参与其中。

与需要智能体之间直接网络连接的 API 协议不同，C2C 通过**人类中转**工作：发送方智能体生成传输包，人类转发，接收方智能体安装。

## C2C 与其他协议的对比

| | C2C | [A2A](https://github.com/a2aproject/A2A) | [MCP](https://github.com/modelcontextprotocol) |
|---|---|---|---|
| **用途** | 技能传授 | 智能体通信 | 工具集成 |
| **传输方式** | 任意文字信道 | HTTP/API | JSON-RPC |
| **人类角色** | 必须中转 | 可选 | 无 |
| **跨平台** | ✅ 任意 IM | ❌ API 端点 | ❌ 本地/API |
| **隐私** | ✅ 加密，点对点 | 取决于实现 | 不适用 |
| **需要网络互通** | ❌ 智能体之间 | ✅ 直接连接 | ✅ 直接连接 |
| **依赖** | 无（系统自带） | SDK + API 服务 | SDK + 运行时 |

**C2C 与 A2A 和 MCP 互补，而非竞争。** A2A 处理实时智能体通信，MCP 处理工具集成，C2C 处理知识传授。

## 快速链接

| 资源 | 链接 |
|---|---|
| 📖 协议规范 | [SPEC.md](SPEC.md) |
| 🦞 参考实现 | [lobster-distill](https://github.com/c2c-protocol/lobster-distill) |
| 🏪 ClawHub | [qiaoy01/lobster-distill](https://clawhub.ai/qiaoy01/lobster-distill) |

## 核心原则

1. **人类参与审核** — 没有人类的明确操作，不会发生任何传输
2. **传输无关** — 在任何能承载文字的信道上工作
3. **零依赖** — 仅使用普遍存在的系统工具（openssl、curl、tar）
4. **默认隐私** — 加密传输、一次一密、存储自动过期

## 开始使用

安装参考实现：

```bash
npx clawhub@latest install qiaoy01/lobster-distill
```

或直接克隆：

```bash
git clone https://github.com/c2c-protocol/lobster-distill.git
```

## 参与贡献

C2C 是一个开放协议。我们欢迎：

- 协议改进提案（提交 Issue）
- 其他框架的替代实现
- 规范翻译
- 安全审查

## 许可证

MIT

---

*知识提纯，加密蒸馏，一瓶送达。* 🦞🧪
