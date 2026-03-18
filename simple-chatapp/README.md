# Simple Chat App（中文说明）

这是一个基于 **Claude Agent SDK** 的最小化聊天演示项目：

- 前端：React + Vite
- 后端：Express + WebSocket
- 能力：支持多会话聊天、展示 Agent 文本回复与工具调用轨迹（`tool_use`）

![架构图](diagram.png)

## 项目定位

这个仓库的目标是展示「如何把 Claude Agent SDK 接入一个前后端聊天应用」，而不是完整生产系统。

它适合：

- 学习 Agent SDK 的接入方式
- 观察流式输出和工具调用过程
- 作为你自己应用的最小骨架

## 核心能力

- ✅ 聊天会话创建与切换
- ✅ WebSocket 实时收发消息
- ✅ 展示 Agent 普通回复（`assistant_message`）
- ✅ 展示 Agent 工具调用过程（`tool_use`）
- ✅ 展示单轮任务完成结果（`result`，包含成功状态、耗时、cost）

## 当前限制（很重要）

- ⚠️ 消息与会话存储在内存中，服务重启后会丢失
- ⚠️ 无用户认证与权限隔离
- ⚠️ 未实现「附件交付链路」（例如让用户点击下载 AI 生成的 PDF/PPT）
  - 当前更偏向：Agent 在后端机器本地生成文件
  - 并不是完整的“面向终端用户的云端文件下载”方案

## 快速开始

### 1) 环境要求

- Node.js 18+
- 可用的 Claude Agent SDK 凭据（设置 `ANTHROPIC_API_KEY`）

### 2) 安装依赖

```bash
npm install
```

### 3) 启动项目

```bash
npm run dev
```

会同时启动：

- 后端（Express + WebSocket）：<http://localhost:3001>
- 前端（Vite + React）：<http://localhost:5173>

浏览器打开：<http://localhost:5173>

## 端到端流程（用户视角）

1. 用户在前端输入消息并发送
2. 前端通过 WebSocket 发给后端
3. 后端将消息交给 Agent SDK
4. SDK 流式返回结果，后端分流并推送给前端：
   - 文本回复：`assistant_message`
   - 工具调用：`tool_use`
   - 轮次结束：`result`
5. 前端按消息类型渲染到聊天窗口

## 目录结构（简版）

```text
simple-chatapp/
├─ client/                 # React 前端
│  ├─ App.tsx              # WebSocket 消息处理与状态管理
│  └─ components/
│     ├─ ChatList.tsx      # 会话列表
│     └─ ChatWindow.tsx    # 消息窗口（含 tool_use 展示）
├─ server/
│  ├─ server.ts            # Express + WebSocket 入口
│  ├─ session.ts           # 会话编排：分流 assistant/tool_use/result
│  ├─ ai-client.ts         # Agent SDK 调用与工具配置
│  └─ chat-store.ts        # 内存聊天存储
└─ README.md
```

## 生产化建议

如果你要把它用于线上环境，建议至少补齐以下能力：

1. **隔离 Agent 运行环境**  
   将 Agent SDK 放到独立容器/服务中，降低 Bash、文件系统和网页抓取能力带来的安全风险。

2. **持久化存储**  
   用数据库替换内存 `ChatStore`，避免重启丢数据。

3. **会话状态持久化与恢复**  
   保存并恢复 SDK 多轮会话所需的 transcript/state。

4. **认证与权限控制**  
   引入用户系统，限制会话和文件访问权限。

5. **附件交付能力（推荐）**  
   对 AI 生成文件实现：
   - 文件注册（`fileId`）
   - 下载 API 或对象存储预签名 URL
   - 前端附件卡片与下载按钮

## 演示

![Demo](demo.gif)
