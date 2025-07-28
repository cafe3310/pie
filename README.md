# Prompt Identifier Embedded (PIE)

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

**PIE (Prompt Information Encoding)** 是一种旨在让 System Prompt 实现**自描述、可移植、可验证**的单行元数据规范。

它通过一个简单、内联的 Markdown 注释，将 Prompt 的身份、版本、作者、依赖、许可等关键信息直接“嵌入”到 Prompt 本身，解决了 Prompt 在流转和协作过程中的信息孤岛问题。

**PIE (Prompt Information Encoding)** is a single-line metadata standard designed to make System Prompts **self-describing, portable, and verifiable**.

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
| **`PROMPT-META`** | `PROMPT-META` | 固定的“魔法字符串”，用于快速识别和搜索 PIE 声明。<br>A fixed "magic string" for quickly identifying and searching for PIE declarations. |
| **核心标识**<br>Core Identifier | `name@YYYY-MM-DD` | **名称**和**更新日期**的组合。日期即版本。<br>A combination of the **name** and **update date**. The date serves as the version. |
| **作者信息**<br>Author Info | `by:author(contact)` | **作者**及其**联系方式**。联系方式自由填写。<br>**Author** and their **contact information**. The contact info is free-form. |
| **来源仓库**<br>Repository | `repo:repo_url` | 该 Prompt 所在的 Git 仓库地址。<br>The Git repository URL where the prompt is located. |
| **授权协议**<br>License | `license:license_id` | 使用的开源协议，建议使用 [SPDX ID](https://spdx.org/licenses/)。<br>The open-source license used, preferably an [SPDX ID](https://spdx.org/licenses/). |
| **工具依赖**<br>Tool Dependencies | `tools:alias=pkg,...` | **(关键功能)** 声明所需的外部工具，支持**别名(alias)**。<br>**(Key Feature)** Declares required external tools, supporting an **alias** system. |
| **目标模型**<br>Target Models | `models:model1,...` | 设计和测试时所使用的目标大模型。<br>The target large models used during design and testing. |


## 5. 辅助工具 / Helper Prompts

您可以使用以下 System Prompt 片段来处理不同的 PIE 任务。

You can use the following System Prompt snippets for various PIE-related tasks.


### 5.1. PIE 环境检查器 (嵌入式片段) / PIE Environment Checker (Embeddable Snippet)

将此片段嵌入到任何需要执行 PIE 检查的 System Prompt 顶部。

```text
[PIE(prompt-identifier-embedded) 检查] 首先解析 `PROMPT-META`:
1. 工具缺失? -> 停止。仅回应: "PIE FAIL: Missing tool: [包名]"。
2. 模型不符? -> 警告: "PIE WARN: Model mismatch."，然后继续。
3. 全部通过? -> 静默继续主任务。
```

Embed this snippet at the top of any System Prompt that needs to perform a PIE check.

```text
[PIE(prompt-identifier-embedded) CHECK] First, parse `PROMPT-META`:
1. Tool missing? -> HALT. Reply ONLY: "PIE FAIL: Missing tool: [package]".
2. Model mismatch? -> WARN: "PIE WARN: Model mismatch.", then CONTINUE.
3. All pass? -> Silently CONTINUE to main task.
```

### 5.2. PIE 详细解释器 / PIE Explainer

使用此 Prompt，让 AI 为您详细解释一个 PIE 字符串的含义。

> 你是一个 PIE(prompt-identifier-embedded) 文档机器人。当用户给你一个 PIE 字符串时(包含 PROMPT-META 关键字)，解析它，并用清晰的 Markdown 表格解释每个字段的含义。

Use this prompt to have an AI explain the meaning of a PIE string in detail.

> You are a PIE(prompt-identifier-embedded) Documentation Bot. When given a PIE string tagged by PROMPT-META, parse it and explain each field in a clear Markdown table.

### 5.3. PIE 到 YAML 转换器 / PIE to YAML Converter

使用此 Prompt，将紧凑的 PIE 格式转换为结构化的 YAML，方便你编辑。

> 你是一个 PIE 到 YAML 的转换器。解析用户给出的 PIE(prompt-identifier-embedded) 字符串，并将其转换为示例中所示的结构化 YAML 格式。现在，请转换用户的 PIE 字符串。
>
> **期望的 YAML 输出结构:**
> ```yaml
> name: workflow-analyzer
> version: '2025-07-15'
> authors:
>   - name: cafe3030
>     contact: github/cafe3310
> repository: github/cafe3310/prompts
> license: CC-BY-NC-4.0
> dependencies:
>   tools:
>     - alias: memory
>       package: npmjs/mcp-server-memories-off
> target_models:
>   - gemini-2.5-pro
> ```

Use this prompt to convert the compact PIE format into structured YAML for editting.

> You are a PIE-to-YAML converter. Parse the given PIE(prompt-identifier-embedded) string and convert it into a structured YAML format as shown in the example. Now, convert the user's PIE string.
>
> **Desired YAML Output Structure:**
> ```yaml
> name: workflow-analyzer
> version: '2025-07-15'
> authors:
>   - name: cafe3030
>     contact: github/cafe3310
> repository: github/cafe3310/prompts
> license: CC-BY-NC-4.0
> dependencies:
>   tools:
>     - alias: memory
>       package: npmjs/mcp-server-memories-off
> target_models:
>   - gemini-2.5-pro
> ```

### 5.4. YAML 到 PIE 转换器 / YAML to PIE Converter

使用此 Prompt，将结构化的 YAML 配置转换回单行的 PIE 字符串。

> 你是一个 YAML 到 PIE 的转换器。接收用户输入的 YAML，并将其压缩为单行的 PIE(prompt-identifier-embedded) 字符串，请遵循所有 PIE 格式规则（如用 `@` 连接版本，用 `()`包裹联系方式，用 `,` 分隔列表等）。现在，请转换用户的 YAML。

Use this prompt to convert structured YAML configuration back into a single-line PIE string.

> You are a YAML-to-PIE converter. Take the user's YAML input and compress it into a single-line PIE(prompt-identifier-embedded) string, following all PIE formatting rules (`@` for version, `()` for contact, `,` for lists, etc.). Now, convert the user's YAML.

## 6. 协议 / License

本项目本身采用 **MIT License**。

PIE 声明中 `license` 字段所指定的协议，仅适用于该 Prompt 内容本身。

This project itself is licensed under the **MIT License**.

The license specified in the `license` field of a PIE declaration applies only to the prompt content itself.

```
MIT License

Copyright (c) 2025 cafe3310

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
