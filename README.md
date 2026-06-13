# Memory Bank Skills

中文 | [English](README.en.md)

这个 skill 的核心作用是：**帮 AI 维护项目记忆，而不是无限追加 memory 文件。**

它解决的问题是：很多项目的 `memory.md`、`memory-bank.md`、上下文记录会越写越长，旧计划、新状态、踩坑、历史流水混在一起。AI 下次读取时，很容易把旧信息当成当前事实。

所以这个 skill 的思想是：

```text
不要记更多，而是让未来的 AI 少读、准读、读当前正确的东西。
```

它也刻意保持得比完整 agent harness 更轻。新版增加了 finish pass，用来在任务结束后判断什么值得记住；但不会引入 `spec.md`，也不会把 memory 变成以后写代码必须遵守的硬性规范。

## 它现在有两个版本

- Claude 版：`claude/memory-bank/`
- Codex 版：`codex/memory-bank-maintainer/`

Claude 版更像命令工具，支持：

```text
status
init
update
archive
finish
```

Codex 版更像自然语言维护助手，适合你说：

```text
帮我更新 memory
整理一下 memory-bank
把旧上下文归档
任务结束了，沉淀一下记忆
把这个踩坑记一下
```

## 它怎么组织记忆

最小结构是：

```text
memory-bank/
  current.md
  knowledge.md
  history/
    YYYY-MM.md
```

含义：

```text
current.md
```

只放当前事实，比如项目现在做到哪、下一步是什么、当前限制是什么。这个文件应该很短，每次恢复上下文优先读它。

```text
knowledge.md
```

放稳定知识，比如长期规则、用户偏好、验证过的命令、重要注意事项。

```text
history/YYYY-MM.md
```

放历史流水，比如某天做了什么、旧方案、旧状态。默认不读，只有复盘或回溯时再读。

项目变复杂后，可以再拆：

```text
commands.md
decisions.md
pitfalls.md
contracts.md
```

分别放命令、决策、踩坑、接口/数据契约。

## Finish Pass

Finish pass 用在一次任务、同步、修复、发布或排查结束后。它不是总结所有细节，而是判断哪些内容未来真的需要。

它会检查：

- 当前状态是否变化。
- 是否产生了稳定知识。
- 是否出现了值得保留的踩坑。
- 是否做了重要决策。
- 是否有旧状态需要移入 history。
- 是否其实没有值得写入长期记忆的内容。

## 它的关键规则

第一，优先替换当前事实，不要一直追加。

比如旧状态是：

```text
当前任务：实现导入功能
```

新状态变成：

```text
当前任务：验证导入功能
```

那就应该替换 `current.md`，而不是在下面继续追加一条新状态。

第二，旧内容要归档或标记。

状态有四类：

```text
active       当前有效
uncertain    未确认
deprecated   已过期，不建议继续用
historical   只作为历史参考
```

这样 AI 不会把三个月前的方案当成现在的真相。

第三，按需读取，不要全读。

比如：

- 问当前进度：读 `current.md`
- 问为什么这么决定：读 `decisions.md`
- 问命令怎么跑：读 `commands.md`
- 问踩过什么坑：读 `pitfalls.md`
- 问历史过程：读 `history/`

## 它不是什么

它不是自动记忆数据库。  
不是 agentmemory 的替代品。  
不是 RAG 或向量记忆系统。  
也不是完整 agent harness。

它更像一套“人工可审查的项目记忆整理规则”。

## 安全规则

它明确禁止保存：

```text
.env 原文
API key
token
cookie
SSH key
私有中转站 key
账号凭证
```

可以保存的是：

```text
环境变量名称
配置用途
脱敏示例
错误类型
决策原因
```

## 示例

查看 `examples/minimal-memory-bank/`，里面展示了如何把一个追加型 `memory.md` 拆成分层的 memory 文件。

查看 `examples/finish-pass/`，里面展示了如何在任务结束后判断哪些内容值得写入长期记忆。

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
