---
name: openclaw-agent-config-wizard
description: >
  当用户说"帮我配置一个专门做 X 的 agent"、"创建一个新 agent"、"设计一个 agent"、
  "配置 agent 人设"、"设置工作区"、"配置多智能体"、"设置 heartbeat"、
  或任何需要引导式问答来创建或配置 OpenClaw agent 的场景时触发。
  Use this skill whenever the user needs guided setup for an OpenClaw agent
  through step-by-step questions with dynamic options.
---

# OpenClaw Agent 配置向导（动态问答式）

通过**单问题 + 动态选项**引导用户完成 agent 配置。

## 交互状态

在对话中维护状态（不需要存储，随时从对话历史推断）：

| 字段 | 说明 |
|------|------|
| current_question | 当前问题编号 |
| answered | 已回答的问题及答案（从历史推断）|
| context | 累积的配置上下文 |

---

## 问题流程

### 问题 1 (P0 - 最优先)
**问题**: 你希望这个 agent 做什么？

**选项**:
- A. 整理和收集数据（如 KOL 数据、爬虫、文件）
- B. 自动化任务执行（如定时任务、CI/CD、部署）
- C. 监控和告警（如服务器状态、日志、预算提醒）
- D. 客服支持（如回答问题、处理请求）
- E. 其他（请说明）

**下一问题**: q2_scope

---

### 问题 2 (P1 - 范围细化)
根据问题1的答案，动态生成问题。

**如果选 A（整理数据）**:
问题: 整理什么类型的数据？

选项:
- A. 视频数据（B站、YouTube 等）
- B. 社交媒体帖子（小红书、微博等）
- C. 文档和文章（公众号、知乎等）
- D. 文件和目录（本地文件、云存储等）

**如果选 B（自动化任务）**:
问题: 执行什么类型的任务？

选项:
- A. 定时任务（每小时/每天/每周执行）
- B. 代码部署（GitHub Actions、CI/CD）
- C. 数据同步（数据库、API 数据同步）
- D. 其他自动化

**如果选 C（监控告警）**:
问题: 监控什么？

选项:
- A. 服务器资源（CPU、内存、磁盘）
- B. 应用状态（服务是否在线、错误日志）
- C. 预算和用量（API 调用量、云服务费用）
- D. 其他监控对象

**如果选 D（客服支持）**:
问题: 提供什么类型的支持？

选项:
- A. 回答用户问题
- B. 处理订单和请求
- C. 引导用户操作
- D. 其他客服场景

**如果选 E（其他）**:
问题: 请简要描述你需要的场景

**下一问题**: q3_channel

---

### 问题 3 (P1 - 频道)
问题: 这个 agent 会用在哪些频道？

**选项**:
- A. 钉钉私聊 (DM)
- B. 钉钉群
- C. WebChat
- D. Telegram
- E. 多频道都要

**下一问题**: q4_frequency

---

### 问题 4 (P2 - 频率)
问题: 这个 agent 多久会使用一次？

选项:
- A. 每天多次（高频使用）
- B. 每天一次
- C. 偶尔使用（按需触发）
- D. 需要定时自动执行

**如果选 D（定时执行）**:
追问: 定时频率是？

选项:
- A. 每小时
- B. 每天（指定时间）
- C. 每周
- D. 自定义（稍后配置）

**下一问题**: q5_memory

---

### 问题 5 (P3 - 记忆)
问题: 是否需要记住跨会话的信息？

选项:
- A. 需要（长期记忆用户偏好、上下文）
- B. 不需要（每次会话独立）
- C. 不确定

**下一问题**: 检查是否达到最小信息量

---

## 停止条件

当收集到以下信息时，停止问答并生成方案草稿：

- ✅ 核心功能（问题1）
- ✅ 范围细化（问题2）
- ✅ 使用频道（问题3）

**可选增强**:
- 频率/定时（问题4）
- 记忆需求（问题5）

---

## 方案生成

基于收集的 context，生成以下配置文件草稿：

### 1. AGENTS.md
```markdown
# [Agent Name] - 工作区配置

## 角色与职责
- 核心功能：[基于问题1的回答]
- 适用范围：[基于问题2的回答]

## 启动规则
- 读取：SOUL.md、IDENTITY.md、USER.md
- 启动时：[根据场景描述]

## 群聊策略（如适用）
- 被动触发时减少噪音
- 仅在明确需要时回复

## 定时任务（如配置）
- 任务类型：[基于问题4]
- 执行频率：[具体时间]
```

### 2. SOUL.md
```markdown
# SOUL.md - 人格与边界

## 核心性格
[根据用户选择的场景定制]

## 沟通风格
- 简洁、直接
- 技术导向

## 边界
- 不做 [根据场景排除的事项]
- 需要确认：[需要用户确认的事项]
```

### 3. IDENTITY.md
```markdown
# IDENTITY.md - 身份标识

## 名称
[用户指定或根据场景生成]

## 角色
[一句话描述]

## Emoji
[根据场景选择]
```

---

## 交互格式规范

### 问题格式
- 使用简短句子
- 直接提问，不加解释
- 选项用字母标记

好例子：
> 这个 agent 要解决什么问题？
> - A. 整理数据
> - B. 自动化任务
> - C. 监控告警

不好例子：
> 我需要问你一些问题来了解你的需求。请问这个agent主要的核心功能和目的是什么呢？

### 状态推断
不需要维护持久状态。从对话历史中推断用户已回答的问题和答案。

### 方案展示
生成方案后，用以下格式展示：

```markdown
## 方案草稿

### Agent 信息
| 项目 | 内容 |
|------|------|
| **Agent ID** | [用户指定] |
| **名称** | [用户指定] |
| **频道** | [选择的频道] |

### 配置文件
| 文件 | 用途 |
|------|------|
| AGENTS.md | 工作区规则 |
| SOUL.md | 人格定义 |
| IDENTITY.md | 身份标识 |
| HEARTBEAT.md | 定时自检（如需要）|

### 定时任务（如有）
| 任务 | 频率 | 说明 |
|------|------|------|
| [任务名] | [频率] | [说明] |

### 频道配置
根据选择的频道，配置对应的 channel plugin：
- 钉钉：配置 appKey/appSecret 或 webhook
- Telegram：配置 bot token
- 其他：请参考对应插件文档

### 下一步

请确认：
1. 有哪些需要修改的地方？
2. 是否需要添加其他配置？
3. 确认后我可以帮你创建这些文件。
```

---

## 技术细节

### 问题优先级
P0 最先问，P3 最后问。 P0 问题缺失则无法生成有效方案。

### 选项动态生成
后续问题的选项基于前序答案生成，不是固定选项表。这要求在生成问题时：
1. 读取前序问题的答案
2. 根据答案组合推断最可能的选项
3. 生成 2-4 个相关选项

### 错误处理
如果用户回答模糊或不确定：
- 简要确认理解
- 提供最有可能是正确的选项
- 必要时提供"其他"选项

---

## ⚠️ 关键注意事项（实施阶段必读）

### 1. Workspace 路径
**必须使用正确路径格式：**
```bash
# ✅ 正确 - 使用 ~/.openclaw/workspace/
~/.openclaw/workspace/<agentId>

# ❌ 错误 - 不要使用 ~/openclaw/workspace/
~/openclaw/workspace/<agentId>  # 这会变成 /Users/<username>/openclaw/workspace/
```

**原因**：`~` 在 shell 中展开为 `/Users/<username>`，但 OpenClaw 的配置目录默认在 `~/.openclaw/`（有 `.`）。

### 2. 创建 Agent 命令
```bash
openclaw agents add <agentId> \
  --workspace ~/.openclaw/workspace/<agentId> \
  --non-interactive
```

### 3. 文件写入路径
写入配置文件时，必须使用**绝对路径**：
```bash
# ✅ 正确
path: $HOME/.openclaw/workspace/<agentId>/AGENTS.md

# ❌ 错误 - 不要使用 ~ 开头（在 write 工具中）
path: ~/openclaw/workspace/<agentId>/AGENTS.md  # 这是错误的！
```

**在 exec 命令中**，`~` 可以展开：
```bash
mkdir -p ~/.openclaw/workspace/<agentId>
```

**在 write 工具中**，必须使用绝对路径：
```json
{"path": "$HOME/.openclaw/workspace/<agentId>/AGENTS.md"}
```

### 4. Cron 任务必须指定 agentId
创建定时任务时，**必须**包含 `agentId` 字段：

```json
{
  "id": "<unique-id>",
  "agentId": "<新创建的agentId>",
  "name": "<任务名称>",
  "schedule": {...},
  "payload": {...},
  "delivery": {...},
  "sessionTarget": "isolated",
  "enabled": true
}
```

**常见错误**：忘记添加 `agentId` 导致定时任务发送到 main agent。

### 5. 频道绑定
```bash
openclaw agents bind --agent <agentId> --bind <channel>:<accountId>
```

**说明**：
- `channel` 是频道类型（如 dingtalk、telegram、wecom 等）
- `accountId` 是该频道的账号标识，**不是** API Key 或 Token
- 如果不确定 accountId，可以先运行 `openclaw channels status` 查看

### 6. 渠道配置失败排查流程

如果渠道配置失败，按以下步骤排查：

**步骤 1：检查插件状态**
```bash
openclaw channels status
```

**步骤 2：检查插件配置**
```bash
openclaw config get channels.<channelName>
```

**步骤 3：搜索 GitHub Issues**
搜索对应渠道插件的 GitHub 仓库：

| 渠道 | GitHub 仓库 |
|------|------------|
| 钉钉 | https://github.com/openclaw/dingtalk |
| 企业微信 | https://github.com/openclaw/wecom |
| Telegram | https://github.com/openclaw/telegram |

搜索关键词：
- `binding failed`
- `accountId` + `not found`
- 具体的错误信息

**步骤 4：查看插件源码**
如果 GitHub 没有解决方案，查看插件源码：
```bash
cat ~/.openclaw/extensions/<plugin-name>/dist/index.js
```

或者查看 OpenClaw 文档：
- https://docs.openclaw.ai/channels

---

## Phase 5: 用户确认后的实施

当用户明确表示**确认 / 就按这个执行 / 落地**时，执行以下步骤：

### 步骤 1：确认 Workspace 路径
使用绝对路径格式：`$HOME/.openclaw/workspace/<agentId>/`

### 步骤 2：创建 Agent
```bash
mkdir -p ~/.openclaw/workspace/<agentId>
openclaw agents add <agentId> \
  --workspace ~/.openclaw/workspace/<agentId> \
  --non-interactive
```

### 步骤 3：写入配置文件
使用绝对路径写入（以 `$HOME` 开头）：
- `$HOME/.openclaw/workspace/<agentId>/AGENTS.md`
- `$HOME/.openclaw/workspace/<agentId>/SOUL.md`
- `$HOME/.openclaw/workspace/<agentId>/IDENTITY.md`
- `$HOME/.openclaw/workspace/<agentId>/HEARTBEAT.md`
- `$HOME/.openclaw/workspace/<agentId>/USER.md`

### 步骤 4：配置频道
根据用户选择的频道配置：

**钉钉**：
1. 在钉钉开放平台创建应用，获取 AppKey 和 AppSecret
2. 配置到 `openclaw.json` 的 `channels.dingtalk` 部分
3. 绑定：`openclaw agents bind --agent <agentId> --bind dingtalk:<accountId>`

**其他频道**：参考 https://docs.openclaw.ai/channels

### 步骤 5：创建定时任务（如有）
使用 `cron action=add` 创建定时任务，**必须包含 `agentId` 字段**。

### 步骤 6：验证
```bash
openclaw agents list
openclaw agents list --bindings
openclaw channels status
```

---

## 实施检查清单

完成创建后，逐项确认：

- [ ] `openclaw agents list` 能看到新 agent
- [ ] Workspace 目录存在于 `~/.openclaw/workspace/<agentId>/`
- [ ] 配置文件已写入正确位置
- [ ] `openclaw agents list --bindings` 显示正确的绑定
- [ ] `openclaw channels status` 显示频道正常
- [ ] 定时任务（如有）包含正确的 `agentId`
- [ ] 用户在对应频道 @bot 有回复

---

## 常见问题

### Q: 钉钉机器人没有回复？
A: 检查以下几点：
1. `openclaw channels status` 是否显示 dingtalk 正常
2. 绑定是否正确：`openclaw agents list --bindings`
3. 是否在钉钉开放平台配置了机器人消息接收地址
4. 钉钉应用的权限是否包含「接收消息」

### Q: 定时任务没有执行？
A: 检查以下几点：
1. `cron list` 查看任务是否存在
2. 检查任务的 `agentId` 是否正确
3. 查看 `cron runs <jobId>` 查看最近执行状态
4. 检查任务的 `state.nextRunAtMs` 是否合理

---

## 参考

- OpenClaw 工作区文档：https://docs.openclaw.ai/concepts/agent-workspace
- 多智能体配置：https://docs.openclaw.ai/concepts/multi-agent
- 渠道配置：https://docs.openclaw.ai/channels
