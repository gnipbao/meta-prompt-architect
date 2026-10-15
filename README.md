# 元提示词架构师 · Meta Prompt Architect

把模糊需求、旧提示词和失败输出，整理成可直接运行、可检查的结构化提示词。

An agent skill for creating, diagnosing, and improving prompts with explicit variables, evidence boundaries, output contracts, and regression cases.

## 适合什么时候用

- 从一个想法开始，写出可复制使用的新提示词。
- 优化已有提示词，逐段定位问题并保留修改对照。
- 分析失败输出，修复编造、遗漏、冲突或输出格式漂移。
- 只审计 system / developer / user instructions，不改写原文。
- 设计 Agent 的变量契约、工具前提、失败分支和完成条件。

## 工作方式

| 模式 | 输入 | 主要交付 |
| --- | --- | --- |
| A · 新建 | 需求和约束 | 新提示词、假设、测试用例、风险 |
| B · 优化 | 旧提示词，可附失败输出 | 指令单元映射、诊断、改写版、修改对照、回归用例 |
| 只读审计 | 原提示词与审计要求 | 问题定位、证据与评分，不输出替代提示词 |

工作流按复杂度读取构建蓝图、审核量表和回归示例；关键缺失达到提问条件时先澄清。输出使用具名变量、可判定约束、异常行为和完成条件。

质量检查包括六项硬门与五维加权评分。静态分数只说明提示词的设计检查结果；实际效果需要在目标模型上运行用例并保存证据。

## 安装到 Codex

需要 Git。在终端运行：

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/gnipbao/meta-prompt-architect.git \
  "${CODEX_HOME:-$HOME/.codex}/skills/meta-prompt-architect"
```

然后新建 Codex 会话，确认 Skill 列表中出现 `meta-prompt-architect`。目标目录已存在时，先检查其中的本地修改，再决定更新；不要覆盖自己的版本。

也可把仓库克隆到其他目录，再将完整文件夹放入所用 Agent 支持的 Skill 目录。必须一并保留 `references/` 与 `examples/`，不能只复制 `SKILL.md`。其他 Agent 的加载方式以其实际支持为准。

## 使用示例

新建提示词：

```text
使用 $meta-prompt-architect：给我写一个单轮提示词，让通用大语言模型
根据团队每周素材生成中文项目周报。读者是管理层，关注风险、决定和下一步；
正文不超过 600 字，不得补造素材中没有的数据。
```

优化旧提示词：

```text
使用 $meta-prompt-architect：优化下面的提示词，先诊断再改写，
保留全部实质修改对照，并提供回归用例。
旧提示词：“你是顶级分析师，请全面分析这家公司，确保所有信息绝对准确。”
目标：根据我提供的公司资料生成一页竞品摘要；缺失事实必须标注未知。
```

诊断失败输出：

```text
使用 $meta-prompt-architect：这份摘要提示词会编造原文没有的数据。
我会提供旧提示词、原资料和一次失败输出。请找根因、做最小修复，
并设计覆盖原失败与原成功行为的复测用例。
```

只做审计：

```text
使用 $meta-prompt-architect：只审计下面的 system prompt，
给出原句定位、问题后果和优先级，不要替我重写。
```

正常交付包含元信息、提示词、设计说明、测试和风险；模式 B 还包含诊断与修改对照。模式 A 可明确要求“只给提示词”。提示词默认长度与精简规则见 [SKILL.md](SKILL.md)。

## 文件结构

```text
meta-prompt-architect/
├── SKILL.md                         # 路由、构建、审核与交付协议
├── agents/openai.yaml               # Codex 展示名称与默认调用
├── references/
│   ├── prompt-blueprint.md          # 变量、层级、七要素与输出骨架
│   └── review-rubric.md             # 证据等级、六项硬门与评分权重
├── examples/
│   └── retest-prompts.md            # 9 组回归输入与期望行为
├── README.md
└── LICENSE
```

## 验证与当前限制

本次发布完成文件完整性、相对引用和明显敏感信息的静态检查，保留原有 5 个 Skill 文件。尚未在目标模型上执行行为回放；[回归用例](examples/retest-prompts.md) 是测试输入与期望行为，不是已通过的测试报告。

- 简单任务的路由提到主文件中的评分摘要，但完整的六项硬门、五维名称与权重位于 [审核量表](references/review-rubric.md)。需要完整评分时，请在请求中明确要求读取该量表。
- 检索、独立审核和人工复核依赖运行时真实可用的资源；缺失时必须说明降级或未验证状态。
- 提示词无法保证零幻觉、事实正确或跨模型稳定；关键事实与外部操作仍需按实际证据和授权核验。

## 贡献

欢迎提交 Issue 或 Pull Request。报告行为问题时，请提供脱敏后的输入、期望行为、实际输出及所用模型/运行环境；不要上传密钥、私人资料或原始私有会话。涉及规则修改时，请同时给出一个原成功用例和一个针对修改的边界用例。

## 许可

[MIT](LICENSE) © 2026 [gnipbao](https://github.com/gnipbao)
