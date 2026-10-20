# Agent Skills

可供不同 AI Agent 使用的技能集合。每个技能以独立目录提供，采用 [Agent Skills 开放格式](https://agentskills.io/specification)，通过 `SKILL.md` 定义用途和工作规则。

当前技能适用于支持该格式的 Cursor、Claude Code、Codex 等 Agent。核心指令不依赖特定平台、MCP、脚本或外部 API；没有原生 Skill 支持的 Agent 也可以在读取指令后使用。

## 技能目录

| 技能 | 用途 | 说明 |
| --- | --- | --- |
| [asd-ste100-zhs-skill](asd-ste100-zhs-skill/SKILL.md) | 消除简体中文技术文档、操作步骤、需求、提示词、工具说明、错误消息和状态报告中的歧义 | 保留原意、条件和约束；信息不足时列出待确认问题 |

## 安装

### npm / npx（推荐）

使用 [Vercel 的 skills CLI](https://github.com/vercel-labs/skills) 安装，无须为每个 Agent 维护不同版本。CLI 需要 Node.js 和 npm，具体版本要求见其文档；技能本身运行时不需要 Node.js。

在**需要使用技能的项目目录**运行，按提示选择 Agent：

```bash
npx skills add leomoon2016/skills --skill asd-ste100-zhs-skill
```

也可以明确指定目标（按需选择一条）：

```bash
# Cursor
npx skills add leomoon2016/skills --skill asd-ste100-zhs-skill --agent cursor

# Claude Code
npx skills add leomoon2016/skills --skill asd-ste100-zhs-skill --agent claude-code

# Codex
npx skills add leomoon2016/skills --skill asd-ste100-zhs-skill --agent codex
```

同时安装到多个 Agent，并使其在本机各项目中可用：

```bash
npx skills add leomoon2016/skills --skill asd-ste100-zhs-skill --agent cursor claude-code codex --global
```

默认安装到当前项目；`--global` 表示用户级安装。CLI 可能使用共享目录和符号链接，实际位置以安装输出为准。需要独立副本时加 `--copy`，其他参数见 [CLI 安装说明](https://github.com/vercel-labs/skills/blob/main/docs/commands.md)。

远程命令读取 GitHub 上已发布的内容。要安装下载或修改后的本地版本，在**本仓库根目录**运行：

```bash
npx skills add . --skill asd-ste100-zhs-skill --agent cursor claude-code codex --global
```

### 手动安装（无需 npm）

1. 在本仓库页面选择 **Code → Download ZIP**，下载并解压。
2. 将整个 `asd-ste100-zhs-skill` 子目录复制到下表中所选的技能目录。保留 `SKILL.md`、`references/` 和 `LICENSE`；若已有同名目录，先备份再替换。
3. 在目标 Agent 中检查技能是否可见；未识别时重新开启会话，并检查其版本是否支持 Agent Skills。

以下是手动安装可用的位置，项目级路径相对于**使用技能的项目根目录**：

| Agent | 项目级技能目录 | 用户级技能目录 |
| --- | --- | --- |
| Cursor | `.cursor/skills/` | `~/.cursor/skills/` |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |
| Codex | `.agents/skills/` | `~/.agents/skills/` |

目录说明见 [Cursor 文档](https://cursor.com/docs/skills)、[Claude Code 文档](https://code.claude.com/docs/en/skills)、[Codex 文档](https://learn.chatgpt.com/docs/build-skills) 和 [skills CLI 的 Agent 路径表](https://github.com/vercel-labs/skills/blob/main/docs/agents.md)。已有 `~/.codex/skills/` 安装时，先确认当前 Codex 能发现它；新增安装优先使用表中的 `.agents/skills/`。Windows 中 `~` 表示用户主目录，通常为 `%USERPROFILE%`。

例如，Cursor 项目级安装后的结构为：

```text
<项目根目录>/
└── .cursor/skills/
    └── asd-ste100-zhs-skill/
        ├── SKILL.md
        ├── LICENSE
        ├── references/
        │   └── examples.md
        └── agents/
            └── openai.yaml  # 可选的 Codex 界面元数据
```

`agents/openai.yaml` 可随目录一起复制；其他 Agent 的核心使用不依赖该文件。

### 安装后核验

在目标 Agent 中使用下面的测试提示。若同名技能安装在多个目录，先核对实际加载路径与 `metadata.version`，确保测试的是刚安装的版本。

```text
请使用 asd-ste100-zhs-skill，报告实际加载的 SKILL.md 路径和 metadata.version。
请读取该技能的 references/examples.md，说明第 3 例保护了什么语义。
然后改写：处理完成后视情况进行通知。
若未发现技能或无法读取文件，请明确说明，不要声称已加载。
```

应能读取技能和示例，并指出第 3 例保留了必要条件、不确定性及不保证送达的限定。最后一句改写应保留未确定的信息，并列出通知执行者、触发条件和接收者的待确认问题。

CLI 安装成功仅说明文件已放到目标位置。实际使用还依赖 Agent 的技能发现设置、文件访问权限，以及可用的模型服务。仅有技能名称出现在列表中，也不能证明正文和参考文件已加载。

## 使用

### 通用提示

安装后可直接描述任务，让 Agent 根据技能说明选择使用；也可以明确指定技能名称：

```text
请使用 asd-ste100-zhs-skill 改写以下中文，消除歧义，保留原意，并标明原文中需要确认的信息：

处理完成后视情况进行通知。
```

需要手动调用时，不同 Agent 的入口如下：

| Agent | 调用方式 |
| --- | --- |
| Cursor | 在 Agent 对话中输入 `/`，搜索并选择 `asd-ste100-zhs-skill` |
| Claude Code | 输入 `/asd-ste100-zhs-skill`，后接需要处理的文本 |
| Codex | 在提示中写入 `$asd-ste100-zhs-skill`，后接需要处理的文本 |
| 其他 Agent | 使用其原生 Skill 入口；没有该入口时，明确要求读取本技能的 `SKILL.md` 后处理文本 |

例如，未提供 Skill 发现机制、但支持读取项目文件的 Agent，可以使用：

```text
请读取 ./asd-ste100-zhs-skill/SKILL.md，按其中的规则改写以下中文：

处理完成后视情况进行通知。
```

将路径换成该 Agent 实际可读的技能位置。若 Agent 无法访问本地文件，可把 `SKILL.md` 正文作为指令提供；需要详细示例时再补充 `references/examples.md`。这种方式由用户显式加载，不提供自动发现能力。

### 预期结果

技能根据原文和已提供的上下文改写。缺少执行者、条件或对象时，不会自行补充业务事实。

**原文：**

> 处理完成后视情况进行通知。

**保守改写：**

> 处理完成后，视情况发送通知。
>
> 待确认：谁发送通知？什么情况需要通知？通知谁？

用户补充这些信息后，才能进一步明确这句话。更多场景见[中文消歧示例](asd-ste100-zhs-skill/references/examples.md)。

该技能主要用于技术表达和操作说明；一般文学、营销文案和仅要求去除 AI 腔的润色不属于其主要范围。

## 来源与适配

`asd-ste100-zhs-skill` 改编自 [danyuchn/asd-ste100-skill](https://github.com/danyuchn/asd-ste100-skill) v0.4.0，借鉴消除歧义和保持语义的原则，按照简体中文表达习惯重新制定规则，去除英文专属规则。

这是独立的中文适配，不代表 ASD-STE100 官方中文标准或合规认证。

## 许可

各技能的许可由其子目录内的 `LICENSE` 文件说明，请在使用或分发前查看对应文件。

`asd-ste100-zhs-skill` 使用 MIT 许可，并保留上游版权声明，详见[技能目录内的 LICENSE](asd-ste100-zhs-skill/LICENSE)。本仓库不对未来加入的其他技能预设统一许可。
