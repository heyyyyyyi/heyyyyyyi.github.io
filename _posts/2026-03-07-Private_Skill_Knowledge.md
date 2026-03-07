---
layout: post
title: "From General Intelligence to Expert Intuition: Building Private Skill Knowledge for Agents"
date: 2026-03-07 00:10 -0400
lang: en
ref: private-skill-knowledge
slug: private-skill-knowledge
permalink: /en/posts/private-skill-knowledge/
tags: [Agent, Private Skills, Industrial AI]
---

> 🌐 Language: [中文](/zh/posts/private-skill-knowledge/) | [English](/en/posts/private-skill-knowledge/)

Through collaborations with industrial partners, we’ve observed a clear gap between the ideal of all-capable general Agents and the complexity of real-world business workflows. Today’s Agents may reason broadly, but they often lack the domain intuition needed for specific industries, companies, and project processes.

To close this gap, engineering experience must be transformed into Private Skills that Agents can learn and call reliably. This is not only a capability upgrade, but also a core lever for commercialization.

### 1. How to Build Private Skills

Private skills are essentially an engineering abstraction of domain knowledge. Key sources include:

- **Customer Tickets:** Extract task patterns, interaction habits, perceived Agent boundaries, and hard production constraints.
- **Engineer Experience:** Capture how engineers recover from Agent errors; codify successful paths as SOPs and failed paths as guardrails.
- **Environment Feedback:** Use logs and execution summaries to understand Agent behavior in real software/physical environments.

**Skill Hierarchy**

We model Agent knowledge as a hierarchical graph:

- **L1 Atomic Tools:** Single-function APIs (e.g., query inventory, run linter).
- **L2 Composite Skills/Workflows:** Logic-driven sequences (e.g., review code -> run unit tests -> generate report).
- **L3 Cognitive Modules:** High-level strategies (e.g., cost-sensitive planners, resource detectors, risk evaluators).

**Abstraction Methods**

- **System Features:** If a capability is foundational (e.g., batch data loading), productize it as a system feature with user-case documentation.
- **Standalone Skills:** If tool-specific (similar to Claude Skills or OpenHands Microagents), package it as `skill.md` or an independent module in a skill library for on-demand mounting.

### 2. How Agents Actually Use Skills

A skill library alone is not enough; selection and composition are the real challenge:

- **Skill Menu & Skill-Aware Planning:** Before execution, the Agent should inspect a skill menu and plan with explicit awareness of expert packages.
- **Human Guidance & Preference Injection:** Users can manually bind preferred skill sets; a Review Pair Agent can also detect error patterns and auto-trigger matching skills.
- **Performance Balance:** Not all knowledge should be skillized. Excess skills can increase token cost and distract reasoning, so benchmark success rates with and without specific skills.

### 3. How Customers Perceive Value

In business settings, transparency directly drives trust:

- **Transparent Reasoning:** Replace generic statuses like "Agent is thinking" with explicit signals such as "Applying [Industry Expert Skill Package]."
- **Marketplace & Gallery:** Offer ready-to-use scenario packs (e.g., financial report generation, chip verification flow) with editable workflow logic.
- **Value Quantification:** Provide periodic business-impact reports, e.g., "The resource detection skill reduced compute cost by 15% this month."

Moving from general intelligence to expert intuition is not about making Agents know everything. It is about making them invoke the right skill at the right moment and consistently produce measurable value.