# 大模型评测全景：从基座跑分到 Agent 全链路，我们到底在测什么？

> 2026 年，大模型评测已经从"一张卷子定生死"进化成了一个复杂的系统工程。本文尝试从基座能力到 Agent 全链路，梳理当前评测体系的主要维度、关键 benchmark，并以 QwenClawBench 为例，深入拆解 Agent 评测的工程实现逻辑。

---

## 一、评测体系现状：一张越来越密的网

2026 年的大模型评测，可以用一句话概括：**跑分速度比模型迭代速度还快**。当 GPT-5.5 在 SWE-bench Verified 上达到 58.6%、GLM-5.1 开源版追到 58.4% 时，这个 benchmark 在两年前设计时，40% 就已经算优秀了。

这意味着什么？意味着**头部模型正在快速逼近现有 benchmark 的天花板**，旧榜单的区分度在下降，新榜单的设计被迫不断加码。评测本身成了一场"军备竞赛"。

当前主流评测可以归入 7 大方向：

| 方向 | 代表性 Benchmark | 现状 |
|------|----------------|------|
| **综合能力** | MMLU/CMMLU、C-Eval、SuperCLUE | 头部模型基本饱和，区分度下降 |
| **代码能力** | SWE-bench Pro/Verified、LiveCodeBench、HumanEval+ | 最卷的领域，接近真实开发场景 |
| **数学推理** | AIME 2025、FrontierMath、ReasoningMath | 垂直细分，抗"记忆刷分" |
| **Agentic / Tools** | TAU3-Bench、MCPMark、QwenClawBench、WildClawBench | **增长最快**，但标准尚未统一 |
| **多模态** | MMMU、MMBench、MathVista | 跨模态理解，场景驱动 |
| **长上下文** | LongBench、RULER、∞Bench | 128K+ 竞赛，RAG 需求倒逼 |
| **安全与对齐** | HarmBench、SafetyBench、AIR-Bench | 合规刚需，但主观性较强 |

> 💡 **选型建议**：选模型时，优先看垂直领域的评测。比如代码看 SWE-bench Pro，数学看 FrontierMath。不要用综合评分做决策——那是平均值，会掩盖专项能力的真实差距。

---

## 二、Agent 评测：从"能调用工具"到"能完成真实任务"

如果说基座评测是"考知识点"，Agent 评测就是"考做事"。它的核心挑战在于：**Agent 的输出来自于一个动态、交互式的执行过程**，而不是一次性生成一段文本。这给评测带来了三个全新维度：

1. **过程可观测**：需要记录 Agent 与环境的完整交互轨迹（event stream / transcript）
2. **交付物可验证**：Agent 最终产出的文件、代码、配置需要被客观检查
3. **环境一致性**：同样的 prompt 在不同环境、不同时间点执行，结果可能不同

### 2.1 OpenClaw 风格 Agent 评测家族

围绕 OpenClaw 这类"本地部署 + 工具调用"的 Agent 框架，已经出现了多个评测基准，各有侧重：

| Benchmark | 发起方 | 核心特点 |
|-----------|--------|----------|
| **QwenClawBench** | 阿里 QwenTeam + Data Team | 真实用户场景分布，100 任务，8 领域，Hybrid 评分 |
| **ClawsBench** | UC Berkeley | 5 个生产力 mock 服务，同时测能力与安全 |
| **WildClawBench** | 上海 AI Lab / InternLM | 60 个真实任务，端到端，6 大类别 |
| **Claw-Eval** | 人大等高校 | 300 个人工验证任务，2159 条评分细则 |
| **ClawGym** | 阿里 / 人大 | 数据合成 + Agent 训练 + 评测，一体化框架 |

这些 benchmark 的共性是：**不再测"Agent 说了什么"，而是测"Agent 做了什么"**。

---

## 三、深入拆解：QwenClawBench 是怎么做的

QwenClawBench 是目前 Agent 评测中工程化程度最高的开源项目之一。它最初是 Qwen3.6-Plus 的内部评测工具，2026 年 4 月开源。GitHub: `github.com/SKYLENAGE-AI/QwenClawBench`。

### 3.1 任务设计：100 个任务，8 个领域

任务的题量分布不是均匀的，而是基于**真实用户使用 OpenClaw 的场景分布**来设计的：

| 领域 | 题数 | 占比 | 典型场景 |
|------|------|------|----------|
| Workflow & Agent Orchestration | 21 | 21% | 工作流编排、cron 任务、技能创建、多 Agent 协调 |
| System Ops & Admin | 20 | 20% | 系统运维、环境配置、故障排查、工作区管理 |
| Knowledge & Memory Management | 15 | 15% | 知识库构建、记忆系统设计、文档管理 |
| Finance & Quant Trading | 10 | 10% | 量化策略回测、套利监控、交易分析 |
| Data Analysis & Modeling | 10 | 10% | 统计分析、数据处理、质量审计 |
| Security & Vulnerability | 9 | 9% | 安全审计、凭证管理、注入防御 |
| Communication & Scheduling | 8 | 8% | 消息通知、日程规划、定时提醒 |
| Research & Info Retrieval | 7 | 7% | 竞品分析、文献检索、技术研究 |

前两大领域占了 41%，因为这就是 OpenClaw 用户最高频的场景。这种"真实用户分布"的设计理念，让评测结果更贴近实际部署时的表现。

### 3.2 单个任务的文件结构

每个任务是一个独立的 Markdown 文件，包含完整的任务定义和评分逻辑：

```
task_xxxx.md
├── YAML Frontmatter（元数据：ID、类别、评分类型、超时、权重）
├── ## Prompt（给 Agent 的用户指令）
├── ## Expected Behavior（期望行为，LLM Judge 的参考）
├── ## Grading Criteria（评分清单）
├── ## Automated Checks（Python 评分代码，exec 动态执行）
└── ## LLM Judge Rubric（LLM 评审维度与标准）
```

任务还配有一个 `assets/` 目录，里面放初始工作区的文件（代码、配置、数据、日志等），评测时拷贝进 Docker 容器。

### 3.3 评分机制：三种模式 + 一个反作弊设计

#### 三种评分模式

| 模式 | 怎么做 | 适用场景 |
|------|--------|----------|
| **Automated** | Python 函数 `grade(transcript, workspace_path)`，验证输出文件、命令结果、代码执行 | 有明确交付物的任务 |
| **LLM Judge** | Claude Opus 4.5 评审 Agent 的行为轨迹，多维度 0.0-1.0 打分 | 需要评估 reasoning quality 的任务 |
| **Hybrid** | 两者加权，默认权重各 50% | 大部分任务用这个 |

#### Hybrid 的惩罚机制（重点！）

QwenClawBench 的默认 Hybrid 模式有一个硬 guardrail：

```
score = w_auto × s_auto + w_llm × s_llm × 𝟙[s_auto ≥ 0.75]
```

**人话**：如果自动评分低于 0.75，LLM Judge 那部分直接清零。

为什么要这么做？因为他们发现 —— **有些 Agent 做不出正确交付物，但 reasoning trajectory 写得特别漂亮，LLM Judge 会因此给高分**。这就是典型的 "fluent but incorrect" 问题，Agent 在"hack"评审。

这个设计非常务实：**如果连基本的交付物检查都没过，推理过程再漂亮也不应该得分**。

可以用 `--simple-scoring` 关闭这个机制，恢复成简单加权平均。但默认是开的。

### 3.4 Automated 评分具体怎么做？

以 task_00076（A 股 Paper Trading 模块）为例，Automated Checks 会：

1. **AST 语法检查**：`ast.parse(content)` 验证代码是否合法
2. **正则静态分析**：检查是否引用了正确的费率文件（`trading_fees.json`），是否排除了过时陷阱（`outdated_fees.json`、`0.00006` 等）
3. **动态执行验证**：`subprocess.run(['python3', '-c', test_code])` 实际 import 模块，调用方法，检查返回值（如 `calculate_transaction_cost(150.0, 1000, 'buy')` 是否 ≈ 40.50）
4. **端到端运行**：`subprocess.run(['python3', file_path])` 跑 `__main__` 块，验证是否崩溃

这不是"训练模型然后测准确率"，而是**代码工程的静态+动态测试**。检查的是：Agent 写的代码能不能跑、对不对、有没有掉进环境里埋的陷阱。

### 3.5 LLM Judge 审什么？

LLM Judge 看到的不是原始 event stream，而是一个**压缩后的 transcript 摘要**：

- Agent 调了什么工具（`exec`、`read`、`write`）
- 工具返回了什么结果
- 用户发了什么消息

Judge 按 Rubric 的多维度标准打分。以同一个 A 股任务为例，Judge 评审三个维度：

1. **Data Source Navigation (35%)**：有没有识别出环境里埋的陷阱数据，选了正确的权威来源
2. **A-Share Domain Accuracy (35%)**：T+1 结算、涨跌停限制、费率公式、板块分类等是否正确
3. **Functional Code Quality (30%)**：代码能不能 import、能不能跑、输出格式对不对

### 3.6 工程实现亮点

| 特性 | 说明 |
|------|------|
| Docker 隔离 | 每个任务独立容器，环境一致、可复现 |
| 并发执行 | 多容器并行，大幅压缩总评测时间 |
| 异常检测 | API 错误、容器崩溃、超时自动标记，不混进分数 |
| 断点续跑 | 中断后能从断点恢复，跳过已完成任务 |
| 安全限制 | Workspace 快照限制在 `/tmp/qwenclawbench` 目录下 |

### 3.7 已公开评测结果

| 模型 | QwenClawBench 分数 |
|------|-------------------|
| Qwen 3.6 Max (preview) | **59.0%** |
| Qwen3.6-Plus | 57.2 |
| Claude Opus 4.5 | 52.3 |

BenchLM 上 top-10 只拉开 7.2 分，说明这个 benchmark 在中高端模型上的区分度比较细腻。

---

## 四、方法论层面的思考

### 4.1 "Real-user-distribution" vs "均匀分布"

QwenClawBench 的任务分布不均匀 —— Workflow 和 System Ops 占了 41%，Research 只占 7%。这不是疏忽，而是刻意设计：

> 评测应该反映真实使用场景，而不是为了"好看"而平均分配。

如果一个用户 80% 的时间在配置环境和编排工作流，评测却只给这些场景 10% 的权重，那评测结果对实际部署的指导价值就会大打折扣。

### 4.2 "过程审" vs "结果查"

Hybrid 评分模式本质上是在回答两个问题：
- **过程审**：Agent 的 reasoning 过程是否合理？有没有走弯路、有没有用对工具？
- **结果查**：Agent 最终交付的代码/文件/配置是否正确、可运行？

惩罚机制（auto < 0.75 则 llm = 0）的哲学是：**结果是底线，过程是加分项**。这个顺序不能反过来。

### 4.3 "埋陷阱"的设计

QwenClawBench 的很多任务在环境里故意放了过时、冲突、错误的数据文件（如 `outdated_fees.json`、`sz000858_daily_adjusted.csv`）。这不是在为难 Agent，而是在模拟真实工作区里常见的"信息噪音" —— Agent 必须学会判断哪个来源是权威的。

### 4.4 异常检测的必要性

大规模 Agent 评测里，基础设施不稳定是常态。如果不把 API 超时、容器崩溃、空 transcript 等异常挑出来，你的分数里会掺一堆"Agent 其实没错，但网络挂了"的噪音。QwenClawBench 的 `lib_anomalies.py` 专门做了这件事，还支持断点续跑和 `--rerun-anomalous`。

---

## 五、选型建议：怎么选 benchmark

如果你是：

| 角色 | 推荐看的 benchmark | 理由 |
|------|-------------------|------|
| **产品经理 / 选型方** | QwenClawBench、WildClawBench | 接近真实使用场景，结果有工程指导价值 |
| **模型开发者** | SWE-bench Pro、FrontierMath、QwenClawBench | 能看到模型在专项和 Agent 场景的真实短板 |
| **Agent 框架开发者** | Claw-Eval、ClawGym、QwenClawBench | 评测粒度细，能帮助定位框架本身的问题 |
| **安全合规** | HarmBench、SafetyBench、ClawsBench | 专门测对抗性和安全边界 |

通用原则：
- 看**垂直领域**的评测，不要只看综合分
- 看评测的**任务来源**（合成 vs 真实用户场景）
- 看评测的**评分机制**（纯规则 vs LLM-as-judge vs Hybrid）
- 看评测是否**开源可复现**

---

## 六、相关资源

- **QwenClawBench**: https://github.com/SKYLENAGE-AI/QwenClawBench
- **ClawGym 论文**: arXiv 2604.26904
- **SKYLENAGE（晓天衡宇）**: https://skylenage.alibaba-inc.com/sla/home
- **BenchLM**: https://benchlm.ai/
- **OpenClaw**: https://github.com/openclaw/openclaw

---

*本文基于对 QwenClawBench GitHub 仓库的代码分析和相关论文引用整理而成。*
