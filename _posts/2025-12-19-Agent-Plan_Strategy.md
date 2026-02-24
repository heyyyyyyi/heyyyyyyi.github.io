---
layout: post
title: "Agent Planning Strategies: From Task Trackers to Dedicated Plan Agents"
date: 2025-12-19 00:10 -0400
lang: en
categories: [AI, Software Architecture, LLM]
tags: [Agent, Planning, Multi-Agent Systems]
---

As task complexity increases, the context window grows longer and longer, often causing agents to "get lost in the middle" and miss the ultimate directive. To mitigate this, various strategies have emerged to provide agents with better guidance on subtasks.

Effective planning typically revolves around three main pillars:
1.  **Sequential Decomposition:** Breaking a complex task into sequential subtasks, guiding the agent to solve problems step-by-step (similar to human problem-solving).
2.  **State Tracking:** Keeping track of subtask accomplishment to ensure the agent doesn't deviate too far from the ultimate goal, while allowing for dynamic plan updates.
3.  **Verification:** Validating the output of each subtask, and sometimes even employing a dedicated step to validate the entire workflow to guarantee trustworthy results.

## The Task Tracker Approach

Currently, a common approach is to instruct the Agent (via prompt engineering) to create a plan at the beginning and providing it with support tools to track progress or update the plan (a "Task List"). This strategy is straightforward and does not introduce significant architectural complexity.

However, based on my experiments, there are several key insights when comparing an Agent with a Task Tracker versus one without:

### 1. The Cost-Benefit Trade-off
The planning stage does not guarantee better performance. In fact, for simpler tasks, the planning stage often fails to increase accuracy while doubling token costs and increasing time consumption. Furthermore, rigid planning can sometimes reduce diversity, preventing the agent from exploring potentially better solutions.

### 2. Failure Modes: Downgrading vs. Bluffing
A fascinating observation is how agents behave when they get "stuck" (i.e., when they cannot finish a subtask either explicitly defined in the plan or implicitly inferred).
* **With Task Tracker (Downgrading):** The agent tends to "downgrade" the subtask. It modifies the plan to bypass the difficulty, opting for a simpler, often inferior path.
* **Without Task Tracker (Bluffing/Hallucinating):** The agent tends to "make up" further steps to cover its failure. For example, if it cannot train a model on real data, it might silently switch to using simulated data or create a flashy website frontend to impress the user, effectively "easing" the user with fancy but hollow results.

### 3. Trustworthiness Issues
The Task Tracker itself is not always reliable. Even when explicitly prompted to verify steps before marking them as "done," agents frequently suffer from **checklist hallucination**. They may mark steps as accomplished when they are not, or only partially finished. Worse, they might silently replan or downgrade the requirements without notifying the user.

**The Core Challenge:** How do we ensure the agent strictly follows the critical path? It might be necessary to enforce **Self-Verification** at each step or introduce a strict validation mechanism to identify which parts of the plan are "critical" and cannot be downgraded.

## The Plan Agent (Planner-Executor Architecture)
To Be Continue ... 