---
layout: post
title: "The Grand Landscape of LLM Evaluation: From Foundation Benchmarks to Full-Stack Agent Evaluation"
date: 2026-05-13 00:10 -0400
lang: en
ref: agent-evaluation-landscape
slug: agent-evaluation-landscape
permalink: /en/posts/agent-evaluation-landscape/
categories: [AI, Agent Systems, Evaluation, Benchmark]
tags: [Agent Evaluation, Benchmark, LLM, QwenClawBench]
---

> 🌐 Language: [中文](/zh/posts/agent-evaluation-landscape/) | [English](/en/posts/agent-evaluation-landscape/)

# The Grand Landscape of LLM Evaluation: From Foundation Benchmarks to Full-Stack Agent Evaluation

> By 2026, LLM evaluation has evolved from a single-test-score game into a complex systems engineering challenge. This article maps the major evaluation dimensions and key benchmarks — from foundational capabilities to full-stack Agent evaluation — and dives deep into the engineering implementation of Agent evaluation using QwenClawBench as a case study.

---

## 1. The Current State: A Rapidly Expanding Network

The state of LLM evaluation in 2026 can be summed up in one sentence: **Benchmark scores are rising faster than model iteration cycles.** When GPT-5.5 achieves 58.6% on SWE-bench Verified and GLM-5.1 open-source catches up to 58.4%, remember that when this benchmark was designed just two years ago, 40% was considered excellent.

What does this mean? **Top-tier models are rapidly approaching the ceiling of existing benchmarks.** The discriminative power of older leaderboards is declining, forcing new benchmarks to constantly raise the bar. Evaluation itself has become an arms race.

Current mainstream evaluations can be grouped into 7 major directions:

| Direction | Representative Benchmarks | Status |
|-----------|--------------------------|--------|
| **General Knowledge** | MMLU/CMMLU, C-Eval, SuperCLUE | Near saturation for top models, decreasing discrimination |
| **Code** | SWE-bench Pro/Verified, LiveCodeBench, HumanEval+ | Most competitive domain, close to real dev scenarios |
| **Math Reasoning** | AIME 2025, FrontierMath, ReasoningMath | Vertically specialized, resistant to memorization |
| **Agentic / Tools** | TAU3-Bench, MCPMark, QwenClawBench, WildClawBench | **Fastest growing**, but standards not yet unified |
| **Multimodal** | MMMU, MMBench, MathVista | Cross-modal understanding, scenario-driven |
| **Long Context** | LongBench, RULER, ∞Bench | 128K+ competition, driven by RAG demands |
| **Safety & Alignment** | HarmBench, SafetyBench, AIR-Bench | Compliance-driven, but subjective |

> 💡 **Selection Tip**: When choosing a model, prioritize domain-specific evaluations. For code, look at SWE-bench Pro; for math, look at FrontierMath. Don't make decisions based on general scores — they are averages that mask real capability gaps.

---

## 2. Agent Evaluation: From "Can Call Tools" to "Can Complete Real Tasks"

If foundation model evaluation is about "testing knowledge," Agent evaluation is about "testing work." Its core challenge: **An Agent's output comes from a dynamic, interactive execution process**, not a one-shot text generation. This introduces three new evaluation dimensions:

1. **Process Observability**: The complete interaction trajectory (event stream / transcript) must be recorded
2. **Deliverable Verifiability**: The Agent's final output files, code, and configurations must be objectively checked
3. **Environment Consistency**: The same prompt may yield different results in different environments or time points

### 2.1 The OpenClaw-Style Agent Evaluation Family

Around OpenClaw-style "local deployment + tool-calling" Agent frameworks, multiple evaluation benchmarks have emerged:

| Benchmark | Creator | Core Features |
|-----------|---------|---------------|
| **QwenClawBench** | Alibaba QwenTeam + Data Team | Real user scenario distribution, 100 tasks, 8 domains, Hybrid scoring |
| **ClawsBench** | UC Berkeley | 5 productivity mock services, tests capability & safety |
| **WildClawBench** | Shanghai AI Lab / InternLM | 60 real-world tasks, end-to-end, 6 categories |
| **Claw-Eval** | Renmin University et al. | 300 human-verified tasks, 2159 scoring rubrics |
| **ClawGym** | Alibaba / Renmin University | Data synthesis + Agent training + evaluation, integrated framework |

The common thread: **They no longer measure "what the Agent said," but "what the Agent did."**

---

## 3. Deep Dive: How QwenClawBench Works

QwenClawBench is one of the most engineering-complete open-source Agent evaluation projects. Originally Qwen3.6-Plus's internal evaluation tool, it was open-sourced in April 2026. GitHub: `github.com/SKYLENAGE-AI/QwenClawBench`.

### 3.1 Task Design: 100 Tasks, 8 Domains

Task distribution is not uniform — it's based on **real user scenario distributions when using OpenClaw**:

| Domain | Count | % | Typical Scenarios |
|--------|-------|---|-------------------|
| Workflow & Agent Orchestration | 21 | 21% | Workflow orchestration, cron tasks, skill creation, multi-agent coordination |
| System Ops & Admin | 20 | 20% | System maintenance, environment setup, troubleshooting, workspace management |
| Knowledge & Memory Management | 15 | 15% | Knowledge base construction, memory system design, document management |
| Finance & Quant Trading | 10 | 10% | Quant strategy backtesting, arbitrage monitoring, trade analysis |
| Data Analysis & Modeling | 10 | 10% | Statistical analysis, data processing, quality auditing |
| Security & Vulnerability | 9 | 9% | Security auditing, credential management, injection defense |
| Communication & Scheduling | 8 | 8% | Message notifications, schedule planning, timed reminders |
| Research & Info Retrieval | 7 | 7% | Competitive analysis, literature retrieval, technical research |

The top two domains occupy 41% because they represent OpenClaw users' highest-frequency scenarios. This "real-user-distribution" design philosophy makes evaluation results more indicative of production deployment performance.

### 3.2 Individual Task File Structure

Each task is an independent Markdown file containing complete task definition and scoring logic:

```
task_xxxx.md
├── YAML Frontmatter (metadata: ID, category, scoring type, timeout, weight)
├── ## Prompt (user instruction for the Agent)
├── ## Expected Behavior (reference for LLM Judge)
├── ## Grading Criteria (scoring checklist)
├── ## Automated Checks (Python scoring code, dynamically exec'd)
└── ## LLM Judge Rubric (LLM evaluation dimensions & standards)
```

Each task also has an `assets/` directory containing initial workspace files (code, config, data, logs, etc.), which are copied into a Docker container during evaluation.

### 3.3 Scoring Mechanism: Three Modes + One Anti-Cheating Design

#### Three Scoring Modes

| Mode | How It Works | Use Case |
|------|-------------|----------|
| **Automated** | Python function `grade(transcript, workspace_path)`, verifies output files, command results, code execution | Tasks with clear deliverables |
| **LLM Judge** | Claude Opus 4.5 evaluates the Agent's behavior trajectory, multi-dimensional 0.0-1.0 scoring | Tasks needing reasoning quality assessment |
| **Hybrid** | Weighted combination, default 50% each | Most tasks |

#### Hybrid Penalty Mechanism

QwenClawBench's default Hybrid mode has a hard guardrail:

```
score = w_auto × s_auto + w_llm × s_llm × 𝟙[s_auto ≥ 0.75]
```

**Translation**: If the automated score is below 0.75, the LLM Judge portion is zeroed out entirely.

Why? Because they found that **some Agents fail to produce correct deliverables but write beautiful reasoning trajectories, leading LLM Judges to give high scores.** This is the classic "fluent but incorrect" problem — Agents "hacking" the evaluation.

This is a pragmatic design: **If the basic deliverable check fails, the reasoning process shouldn't earn points regardless of how good it looks.**

Use `--simple-scoring` to disable this mechanism and revert to simple weighted averaging. But it's enabled by default.

### 3.4 How Automated Scoring Works in Practice

Using task_00076 (A-Share Paper Trading Module) as an example, Automated Checks will:

1. **AST Syntax Check**: `ast.parse(content)` to verify code validity
2. **Regex Static Analysis**: Check for correct fee file references (`trading_fees.json`), exclusion of outdated traps (`outdated_fees.json`, `0.00006`, etc.)
3. **Dynamic Execution Verification**: `subprocess.run(['python3', '-c', test_code])` to actually import the module, call methods, verify return values (e.g., `calculate_transaction_cost(150.0, 1000, 'buy')` ≈ 40.50)
4. **End-to-End Run**: `subprocess.run(['python3', file_path])` to execute `__main__` block and verify no crashes

This isn't "train a model and measure accuracy" — it's **static + dynamic testing for software engineering**. It checks: Can the Agent's written code run? Is it correct? Did it fall for the traps planted in the environment?

### 3.5 What Does the LLM Judge Evaluate?

The LLM Judge doesn't see the raw event stream — it sees a **compressed transcript summary**:

- What tools the Agent called (`exec`, `read`, `write`)
- What the tools returned
- What messages the user sent

The Judge scores against multi-dimensional rubric standards. Using the same A-Share task as an example:

1. **Data Source Navigation (35%)**: Did the Agent identify the trap data files in the environment and choose the correct authoritative source?
2. **A-Share Domain Accuracy (35%)**: Correctness of T+1 settlement, price limits, fee formulas, sector classification, etc.
3. **Functional Code Quality (30%)**: Can the code be imported? Does it run? Is the output format correct?

### 3.6 Engineering Highlights

| Feature | Description |
|---------|-------------|
| Docker Isolation | Each task in its own container, consistent and reproducible |
| Concurrent Execution | Multiple containers in parallel, significantly reducing total evaluation time |
| Anomaly Detection | API errors, container crashes, timeouts auto-tagged and excluded from scores |
| Resume Support | Can resume from breakpoints, skipping completed tasks |
| Security Constraints | Workspace snapshots restricted to `/tmp/qwenclawbench` directory |

### 3.7 Published Evaluation Results

| Model | QwenClawBench Score |
|-------|--------------------|
| Qwen 3.6 Max (preview) | **59.0%** |
| Qwen3.6-Plus | 57.2 |
| Claude Opus 4.5 | 52.3 |

The top-10 on BenchLM are separated by only 7.2 points, indicating this benchmark has fine-grained discrimination among mid-to-high-end models.

---

## 4. Methodological Reflections

### 4.1 "Real-User-Distribution" vs "Uniform Distribution"

QwenClawBench's task distribution is uneven — Workflow and System Ops account for 41%, while Research is only 7%. This isn't an oversight; it's by design:

> Evaluation should reflect real usage scenarios, not allocate evenly for the sake of "looking good."

If a user spends 80% of their time configuring environments and orchestrating workflows, but the evaluation only gives these scenarios 10% weight, the evaluation results offer little guidance for real-world deployment.

### 4.2 "Process Review" vs "Result Check"

The Hybrid scoring model essentially answers two questions:
- **Process Review**: Is the Agent's reasoning process reasonable? Did it take unnecessary detours? Did it use the right tools?
- **Result Check**: Are the Agent's final deliverables (code/files/configs) correct and runnable?

The penalty mechanism's (auto < 0.75 → llm = 0) philosophy: **Results are the baseline, process is a bonus.** This order cannot be reversed.

### 4.3 "Planted Traps" Design

Many QwenClawBench tasks intentionally include outdated, conflicting, or incorrect data files in the environment (e.g., `outdated_fees.json`, `sz000858_daily_adjusted.csv`). This isn't to make things hard for the Agent — it's to simulate the "information noise" common in real workspaces. Agents must learn to determine which source is authoritative.

### 4.4 The Necessity of Anomaly Detection

In large-scale Agent evaluation, infrastructure instability is the norm. Without filtering out anomalies like API timeouts, container crashes, and empty transcripts, your scores will be contaminated with noise from "the Agent was right but the network was down." QwenClawBench's `lib_anomalies.py` handles this explicitly, and supports resume with `--rerun-anomalous`.

---

## 5. Selection Guide: How to Choose a Benchmark

| Role | Recommended Benchmarks | Rationale |
|------|----------------------|-----------|
| **Product Manager / Evaluator** | QwenClawBench, WildClawBench | Close to real usage scenarios, results have engineering guidance value |
| **Model Developer** | SWE-bench Pro, FrontierMath, QwenClawBench | Reveals real weaknesses in specific and Agent scenarios |
| **Agent Framework Developer** | Claw-Eval, ClawGym, QwenClawBench | Fine-grained evaluation, helps locate framework-level issues |
| **Security & Compliance** | HarmBench, SafetyBench, ClawsBench | Specifically tests adversarial robustness and safety boundaries |

General Principles:
- Look at **domain-specific** evaluations, not just composite scores
- Check the **task source** (synthetic vs real user scenarios)
- Understand the **scoring mechanism** (pure rule-based vs LLM-as-judge vs Hybrid)
- Verify if the benchmark is **open-source and reproducible**

---

## 6. Related Resources

- **QwenClawBench**: https://github.com/SKYLENAGE-AI/QwenClawBench
- **ClawGym Paper**: arXiv 2604.26904
- **SKYLENAGE**: https://skylenage.alibaba-inc.com/sla/home
- **BenchLM**: https://benchlm.ai/
- **OpenClaw**: https://github.com/openclaw/openclaw

---

*Based on code analysis of the QwenClawBench GitHub repository and related papers.*
