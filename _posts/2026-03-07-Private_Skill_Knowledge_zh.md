---
layout: post
title: 从通用智能到专家直觉：构建 Agent 的私域技能知识库
date: 2026-03-07 00:10 -0400
lang: zh
ref: private-skill-knowledge
slug: private-skill-knowledge
permalink: /zh/posts/private-skill-knowledge/
tags: [Agent, Private Skills, Industrial AI]
---

> 🌐 语言 / Language: [中文](/zh/posts/private-skill-knowledge/) | [English](/en/posts/private-skill-knowledge/)

在与工业界客户的深度合作中，我们发现了一个显著鸿沟：理想中“无所不能”的通用 Agent，与现实中复杂多变的业务需求之间存在明显断层。当前 Agent 虽具备广泛能力，但往往缺乏对特定行业、公司与项目流程的深度理解。

为了填补这道鸿沟，我们需要将工程师的实战经验转化为 Agent 可学习、可调用的私域技能（Private Skills）。这不仅是提升 Agent 效率的关键，也是商业化落地的核心竞争力。

### 一、如何构建私域技能（How to Build Private Skills）

私域技能的本质，是行业知识的抽象与工程化。其信息来源主要包括：

- **客户反馈（Customer Tickets）：** 挖掘客户任务输入、交互习惯、对 Agent 边界的感知，以及工业级硬性要求。
- **工程师经验（Engineer Experience）：** 记录工程师修复 Agent 失误的过程，将成功路径沉淀为 SOP，将失败路径抽象为“禁忌项”或“避坑指南”。
- **环境反馈（Environment Feedback）：** 通过运行日志与执行总结，分析 Agent 在真实物理/软件环境中的表现。

**技能的抽象与层级**

我们将 Agent 的知识结构视为层级图谱（Hierarchical Graph）：

- **L1 原子工具（Atomic Tools）：** 单一功能 API（如查询库存、运行 Linter）。
- **L2 复合技能/工作流（Composite Skills/Workflows）：** 带逻辑判断的序列（如先评审代码，再运行单测，最后输出报告）。
- **L3 认知模块（Cognitive Modules）：** 高阶策略能力（如成本敏感型规划器、资源探测器、风险评估专家）。

**抽象形式**

- **系统功能：** 若属于底层能力（如大批量数据加载），应固化为系统特性，并配套 User Case 文档。
- **独立技能：** 若属于特定工具（类 Claude Skills 或 OpenHands Microagents），则封装为 `skill.md` 或独立包，存入技能库供用户按需挂载。

### 二、Agent 如何真正使用技能（How Agents Use Skills）

拥有技能库只是第一步，真正难点在于“如何选择”与“如何组合”：

- **技能菜单与感知规划：** 执行前读取“技能清单”，并在 Planning 阶段具备“技能意识”，判断哪些步骤应调用专家经验包。
- **人工引导与偏好注入：** 用户可按经验手动指定技能集；同时引入“评审对等 Agent（Review Pair Agent）”，通过错误模式识别自动触发对应技能。
- **性能平衡：** 并非所有知识都应技能化。过多技能会增加 Token 消耗并干扰自主推理，应通过 Benchmark 对比“开启/关闭技能”的成功率，确保技能是加速器而非噪声源。

### 三、如何让客户感知价值（How Customers Perceive Value）

在商业场景中，透明度直接决定客户信任度：

- **透明化推理（Transparent Reasoning）：** UI 不应只显示“正在思考”，而应明确标注“正在调用【XX 工业专家经验包】优化方案”。
- **技能超市与模板库（Marketplace & Gallery）：** 提供开箱即用的场景包（如金融研报生成、芯片验证流），并允许客户微调工作流逻辑节点。
- **价值量化报告（Value Quantification）：** 定期推送报告，例如“本月通过【资源探测技能】为您节省了 15% 的计算成本”。

从通用智能走向专家直觉，关键并不在于让 Agent 知道更多，而在于让它在正确场景下调用正确技能，并持续创造可衡量的业务价值。