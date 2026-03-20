# openclaw-agent-config-wizard

OpenClaw Agent 配置向导 skill —— 通过结构化问答帮用户设计、搭建或多智能体拆分。

## 安装

```bash
npx skills add ThomasLiu/openclaw-agent-config-wizard@latest
```

## 功能

- **问答式需求挖掘**：3～6 个问题引导，附选项示例
- **单/多 Agent 架构建议**：对比表清晰展示 agentId、用途、workspace、绑定
- **完整工作区文件草案**：AGENTS.md、SOUL.md、IDENTITY.md、TOOLS.md、HEARTBEAT.md、USER.md
- **Skills 生态推荐**：搭配 `find-skills` 检索真实可安装 skill，禁止臆造包名
- **CLI 落地步骤**：确认后输出可直接执行的命令

## 使用场景

- `帮我配一个专门做 X 的 agent`
- `OpenClaw 配置`、`agent 人设`、`工作区`、`多智能体`
- `频道绑定`、`heartbeat`、`skills`、从零搭助手
- 需要人机问答式引导，不接受一步到位的模糊需求

## 文件结构

```
openclaw-agent-config-wizard/
├── SKILL.md                        # 主 skill 文件
├── references/
│   ├── external-skills.md         # 生态 skill 检索规范
│   ├── openclaw-paths.md          # 路径与 CLI 速查
│   └── conversation-patterns.md    # 问答模板与术语表
└── evals/
    └── evals.json                 # 测试用例
```

## 相关链接

- [OpenClaw 文档](https://docs.openclaw.ai)
- [skills.sh 生态](https://skills.sh/)
