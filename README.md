# Prompt Identifier Embedded (PIE) 🥧

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

一个用于 System Prompt 的、自文档化的单行元数据标准。

A single-line, self-documenting metadata standard for System Prompts.

---

## 1. 快速上手 / Quick Start

在您的 System Prompt 中间（例如「角色声明」之后）添加这样一行注释：

Add this comment line to your System Prompt, after role assignment:

> `你是一个有用的助手... / You are a helpful assistant...`
>
> `[//]: # PROMPT-META (your-prompt-name@2025-07-15; by:your-name(github/your-gh-id); repo:github/your-gh-id/your-repo; license:CC-BY-NC-4.0; tools:memory=npmjs/mcp-server-memories-off,obsidian=npmjs/obsidian-mcp-tools,tool-name=source/package-name; models:gemini-2.5-pro,gpt-4o,usable-models)`
>
> `## 任务要求: / ## Tasks:`



## 2. 这是什么？/ What is it?

**PIE (Prompt Identifier Embedded)** 是一种旨在让 System Prompt 实现**自描述、可移植、可验证**的单行元数据规范。

它通过一个简单、内联的 Markdown 注释，将 Prompt 的身份、版本、作者、依赖、许可等关键信息直接“嵌入”到 Prompt 本身，解决了 Prompt 在流转和协作过程中的信息孤岛问题。

**PIE (Prompt Identifier Embedded)** is a single-line metadata standard designed to make System Prompts **self-describing, portable, and verifiable**.

It embeds a prompt's critical metadata—identity, version, author, dependencies, license—directly within it using a simple inline Markdown comment, tackling the loss of origin and version data during sharing and collaboration.

## 3. 为什么需要 PIE？/ Why PIE?

随着 Prompt 变得越来越复杂，我们面临着一系列新的挑战：

*   **版本混乱**: "哪个是最新版？" "这个 Prompt 是为 GPT-3.5 还是 GPT-4 优化的？"
*   **隐式依赖**: Prompt 可能需要特定的工具 (Tools/Functions) 才能工作，但这些依赖关系没有被明确声明。
*   **来源不清**: 很难追溯一个优秀的 Prompt 是谁创作的，来自哪个项目，以及它的使用许可。
*   **管理困难**: 当拥有数十上百个 Prompt 时，无法对其进行有效的分类、搜索和自动化管理。

PIE 通过一个精简且不干扰 LLM 工作的格式，尝试解决以上所有问题。

As prompts become increasingly complex, we face a new set of challenges:

*   **Version Chaos**: "Which is the latest version?" "Was this prompt optimized for GPT-3.5 or GPT-4?"
*   **Implicit Dependencies**: A prompt might require specific tools (Functions) to work, but these dependencies are not explicitly declared.
*   **Unclear Origin**: It's hard to trace who created a great prompt, which project it came from, or what its usage license is.
*   **Management Difficulty**: When you have tens or hundreds of prompts, it's impossible to effectively categorize, search, and manage them automatically.

PIE attempts to solve all of the above problems using a compact format that avoids distracting LLMs.

## 4. 格式说明 / Format Specification

PIE 遵循一个简单、由分号分隔的键值对结构。

PIE follows a simple key-value structure, separated by semicolons.

**通用模板 / General Template:**

`[//]: # PROMPT-META (name@YYYY-MM-DD; by:author(contact); repo:repo_url; license:license_id; tools:alias=package,...; models:model1,...)`

**字段详解 / Field Details:**

| 字段 / Field | 格式 / Format | 描述 / Description |
| :--- | :--- | :--- |
| `PROMPT-META` | `PROMPT-META` | 固定的“魔法字符串”，用于快速识别和搜索 PIE 声明。<br>A fixed "magic string" for quickly identifying and searching for PIE declarations. |
| **主标识**<br>Core Identifier | `name@YYYY-MM-DD` | **名称**和**更新日期**的组合。日期即版本。<br>A combination of the **name** and **update date**. The date serves as the version. |
| **作者信息**<br>Author Info | `by:author(contact)` | **作者**及其**联系方式**。联系方式自由填写。<br>**Author** and their **contact information**. The contact info is free-form. |
| **来源仓库**<br>Repository | `repo:repo_url` | 该 Prompt 所在的 Git 仓库地址。<br>The Git repository URL where the prompt is located. |
| **授权协议**<br>License | `license:license_id` | 使用的开源协议，建议使用 [SPDX ID](https://spdx.org/licenses/)。<br>The open-source license used, preferably an [SPDX ID](https://spdx.org/licenses/). |
| **工具依赖**<br>Tool Dependencies | `tools:alias=pkg,...` | **(关键功能)** 声明所需的外部工具，支持**别名(alias)**。<br>**(Key Feature)** Declares required external tools, supporting an **alias** system. |
| **目标模型**<br>Target Models | `models:model1,...` | 设计和测试时所使用的目标大模型。<br>The target large models used during design and testing. |


## 5. 辅助工具 / Helper Prompts

您可以使用以下 System Prompt 片段来处理不同的 PIE 任务。

You can use the following System Prompt snippets for various PIE-related tasks.

### 5.1. PIE 解读器 / PIE Explainer

使用此 Prompt，让 LLM 为您详细解释一个 PIE 字符串的含义。

```markdown
你是 🥧PIE 解读器🥧。

[//]: # PROMPT-META (pie-expl@2024-07-25; by:cafe3310(github/cafe3310); repo:github/cafe3310/pie; license:MIT; tools:; models:Gemini-2.5-Flash)

## 你的目标

用户提供他 Prompt 中的 PIE 字符串，你来提供清晰的解释。

## 相关知识

PIE(prompt-identifier-embedded) 是一种单行 Prompt 元数据标准，在 https://github.com/cafe3310/pie 中有详细定义和说明，遵循以下格式：

`[//]: # PROMPT-META ($name@$YYYY-MM-DD; by:$author($contact); repo:$repo_desc; license:$license_id; tools:$alias=$pkg_desc,...; models:$best_model,...)`

## 你的任务

1. 提示用户提供给你包含 `PROMPT-META` 的 PIE 字符串。
2. 合理解读缩写 `contact`, `repo_desc`, `pkg_desc` (例如 `https://github.com/a/b` <- `github/a/b`)。
3. 通过列表解读 PIE 字符串中的所有信息，不要遗漏。
```

Use this prompt to have an LLM explain the meaning of a PIE string in detail for you.

```markdown
You are the 🥧PIE Explainer🥧.

[//]: # PROMPT-META (pie-expl@2024-07-25; by:cafe3310(github/cafe3310); repo:github/cafe3310/pie; license:MIT; tools:; models:Gemini-2.5-Flash)

## Your Goal

The user will provide a PIE string from their prompt, and you will provide a clear explanation.

## Knowledge Base

PIE (Prompt Identifier Embedded) is a single-line prompt metadata standard, with detailed definitions and instructions at https://github.com/cafe3310/pie, following this format:

`[//]: # PROMPT-META ($name@$YYYY-MM-DD; by:$author($contact); repo:$repo_desc; license:$license_id; tools:$alias=$pkg_desc,...; models:$best_model,...)`

## Your Task

1. Ask the user to provide you with the PIE string containing `PROMPT-META`.
2. Intelligently interpret abbreviations for `contact`, `repo_desc`, and `pkg_desc` (e.g., expand `github/a/b` to `https://github.com/a/b`).
3. Explain all the information from the PIE string in a list format, without omitting any details.
```

### 5.2. PIE 生成器 / PIE Generator

使用此 Prompt，让 LLM 为你生成 PIE 字符串。

```markdown
你是 🥧PIE 生成器🥧。

[//]: # PROMPT-META (pie-gen@2024-07-25; by:cafe3310(github/cafe3310); repo:github/cafe3310/pie; license:MIT; tools:; models:Gemini-2.5-Flash)

## 你的目标

帮用户创建用于标识他 Prompt 的 PIE 字符串。

## 相关知识

PIE(prompt-identifier-embedded) 是一种单行 Prompt 元数据标准，在 https://github.com/cafe3310/pie 中有详细定义和说明，遵循以下格式：

`[//]: # PROMPT-META ($name@$YYYY-MM-DD; by:$author($contact); repo:$repo_desc; license:$license_id; tools:$alias=$pkg_desc,...; models:$best_model,...)`

## 你的任务

1. 询问或从用户输入中理解: `name`, `author`, `contant`, `repo_desc`, `license_id`, `tools`, `models`。
2. 合理缩写 `repo_desc`, `pkg_desc` (例如 `https://github.com/a/b` -> `github/a/b`)。
3. 结合所有信息和今天的日期，构建 PIE 字符串。
4. 在代码块中输出结果。
```

Use this prompt to have an LLM generate a PIE string for you.

```markdown
You are the 🥧PIE Generator🥧.

[//]: # PROMPT-META (pie-gen@2024-07-25; by:cafe3310(github/cafe3310); repo:github/cafe3310/pie; license:MIT; tools:; models:Gemini-2.5-Flash)

## Your Goal

Help the user create a PIE string to identify their prompt.

## Knowledge Base

PIE (Prompt Identifier Embedded) is a single-line prompt metadata standard, with detailed definitions and instructions at https://github.com/cafe3310/pie, following this format:

`[//]: # PROMPT-META ($name@$YYYY-MM-DD; by:$author($contact); repo:$repo_desc; license:$license_id; tools:$alias=$pkg_desc,...; models:$best_model,...)`

## Your Task

1. Ask for or understand from the user's input: `name`, `author`, `contact`, `repo_desc`, `license_id`, `tools`, and `models`.
2. Intelligently abbreviate `repo_desc` and `pkg_desc` (e.g., shorten `https://github.com/a/b` to `github/a/b`).
3. Combine all the information with today's date to construct the PIE string.
4. Output the result in a code block.
```

## 6. 协议 / License

本项目本身采用 **MIT License**。

PIE 声明中 `license` 字段所指定的协议，仅适用于该 Prompt 内容本身。

This project itself is licensed under the **MIT License**.

The license specified in the `license` field of a PIE declaration applies only to the prompt content itself.
