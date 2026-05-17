# Memory Bank Skills

中文 | [English](README.md)

用于维护项目 Memory Bank 的轻量 skill，核心目标是让项目记忆保持可人工审查、可迁移、不过度占用上下文。

核心思想很简单：

- 当前事实要短。
- 稳定知识要和历史记录分开。
- 过期记忆要明确标记，不要让旧笔记看起来仍然有效。
- Agent 应该按需读取记忆，而不是默认全读。
- 不要保存原始密钥或敏感凭证。

这不是自动记忆系统，也不是向量记忆或 agent 数据库的替代品。它是一套保守的工作流，用来让项目上下文更干净、更可控、更容易审查。

## 包含的 Skill

### Claude Code

路径：

```text
claude/memory-bank/
```

这个版本偏命令型，支持 `status`、`init`、`update`、`archive` 这类动作分类，适合 Claude 风格的 skill 或 slash command 工作流。

### Codex

路径：

```text
codex/memory-bank-maintainer/
```

这个版本偏自然语言触发，适合 Codex skill。它重点识别“更新 memory”“整理 memory bank”“归档旧上下文”“标记过期知识”等请求。

## 推荐的 Memory 结构

先从最小结构开始：

```text
memory-bank/
  current.md
  knowledge.md
  history/
    YYYY-MM.md
```

只有当项目确实需要时，再增加更多文件：

```text
memory-bank/
  current.md
  knowledge.md
  commands.md
  contracts.md
  decisions.md
  pitfalls.md
  history/
    YYYY-MM.md
```

## 安全规则

不要在 memory 文件里保存原始敏感信息：

- `.env` 原文
- API keys
- tokens
- cookies
- SSH keys
- 私有中转站 key
- 账号凭证

可以保存环境变量名称、配置用途、非敏感示例、错误类型和决策原因。

## 示例

查看 `examples/minimal-memory-bank/`，里面展示了如何把一个追加型 `memory.md` 拆成分层的 memory 文件。

## 安装

把对应 agent 的 skill 目录复制到本地 skill 文件夹。

Claude 风格用法：

```text
claude/memory-bank/ -> .claude/skills/memory-bank/
```

Codex 风格用法：

```text
codex/memory-bank-maintainer/ -> .codex/skills/memory-bank-maintainer/
```

如果你的 agent 支持从项目本地加载 skill，也可以直接使用仓库里的目录。

## License

MIT
