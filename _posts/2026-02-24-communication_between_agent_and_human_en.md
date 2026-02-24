---
layout: post
title: "A New Human-Agent Paradigm: Collaboration and Boundary Design in Software Engineering"
date: 2026-02-24 00:10 -0400
lang: en
ref: communication-between-agent-and-human
slug: communication-between-agent-and-human
permalink: /en/posts/communication-between-agent-and-human/
tags: [Agent, Human-AI Collaboration, Agentic Workflow]
---

> 🌐 Language: [中文](/zh/posts/communication-between-agent-and-human/) | [English](/en/posts/communication-between-agent-and-human/)

The rapid application of AI algorithms in engineering practice has brought about a series of pressing issues.

On the one hand, for industries requiring strict adherence to regulatory standards, current AI applications often lack the support of professional data, deep integration with traditional toolchains, and industry certification mechanisms. On the other hand, large models based on the Transformer architecture are inherently limited by their probabilistic nature. Not only is it difficult to meet the 100% accuracy requirement of the industrial sector, but AI "hallucinations" are also hard to accurately detect and control. Furthermore, the limited Context Window inevitably leads to "amnesia" or context forgetting in Agents.

Therefore, to address these challenges in vertical and deep applications, **we must introduce a "Validator" mechanism for Agents.** Code output by an Agent cannot flow directly into the production environment; it must undergo strict verification by rule-based traditional toolchains.

### The Dramatic Shift in the Collaboration Environment: From "Human-Human" to "Human-Agent"

In our cooperation with enterprise teams, we found that the introduction of AI Agents brings a completely new collaboration environment. It has evolved from simple "human-to-human" interactions to complex interactions involving "humans and humans" as well as "humans and Agents (or Agent systems)."

In traditional, non-Agent development, there is a basic consensus among developers: minimize changes to the environments and code of others. Module owners can quickly locate and coordinate issues during subsequent adjustments and debugging based on pre-defined roles adapted to human division of labor.

However, when Agents participate in the development environment, this original consensus is broken, leading to several significant pain points:

1. **Lack of Boundaries and a Propensity for Refactoring:** The prompt defined by the Agent system usually cannot encapsulate all development consensus (limited by Context length and system flexibility). When handling long-chain tasks, Agents easily lose track of the big picture. They tend to make overall changes and global refactorings rather than applying local patches. This makes subsequent tracking extremely difficult and drives code review costs through the roof.
2. **Lack of Proactive Communication and Intermediate Validation:** During development, Agents typically start working immediately upon receiving a user request, regardless of whether the information is sufficient. Requirements deviations that occur in the middle of the process are often not discovered until the final acceptance stage. Agents do not proactively verify requirements or conduct intermediate stage validations like humans do.
3. **Noise Discrepancy in Information Processing:** The "focus" when an Agent processes a piece of information is often different from that of a human developer, resulting in information noise during requirements alignment. Developers often mistakenly believe that as long as a prompt is provided, the Agent will execute it meticulously. However, due to the Agent's own memory mechanisms, prompt design, and the inherent randomness of LLMs, its actual performance is highly "dynamic."

### Exploring Human-Machine Collaboration: Splitting Single-User and Multi-User Scenarios

Currently, humans remain the core of the development environment. The human advantage lies in subjectivity and creative thinking; as the provider of ultimate requirements, it is natural for humans to define primary/secondary needs and constraints. The Agent's advantage lies in summarizing massive amounts of knowledge, its sheer breadth, and its ability to tirelessly execute mechanical steps.

Based on different task natures and complexities, we explored two collaboration scenarios:

* **Scenario 1: Single Human + Agent.** Suitable for tasks with relatively singular requirements and low modification frequencies. Typically, the human proposes the requirement, and the Agent executes continuously step-by-step based on the information and environmental feedback. At an appropriate time (based on human judgment, task completion, or stage summary), the human generates new requirements based on the feedback, continuing multi-round iterations. In this scenario, the human acts as the requirement provider and acceptor, mainly responsible for correcting the Agent's output, and rarely participates in manual development.
* **Scenario 2: Multiple Humans + Agent.** Suitable for collaborative tasks with complex requirements and frequent alignment. Human developers usually have modules they excel at (frontend, backend, architecture, etc.) and assume corresponding responsibilities. **The contradiction lies here: developers have clear boundary responsibilities, while Agents do not.** An Agent might fail to meet the professional standards of a specific module, or secretly break someone else's module. Moreover, in parallel development, solving the tricky problem of "synchronizing communication between Agents used by different developers" is required. Here, humans must also act as "communication bridges," responsible for splitting requirements, handling conflicts, and testing module integration.

### Two System-Level Workflows: Module Development vs. Relay Development

To resolve the above issues, we have preliminarily outlined two Agent collaboration workflows. The core idea for both is to **downgrade complex work into a workflow similar to "Scenario 1".**

#### 1. Module-based Development
* **Pattern:** A developer pairs with a corresponding Agent, defining clear sub-requirements and responsibility boundaries. Work modules (frontend, backend, architecture, etc.) are still divided according to their respective roles, followed by mutual collaboration.
* **Characteristics:** Supports parallel development; requires version control assistance.
* **Limitations & Countermeasures:** It is still limited by the developer's own constraints and cannot fully utilize the Agent's knowledge breadth. This essentially downgrades the Agent to an "Advanced Copilot." To utilize it better, **we must introduce strict permission and scope control.** For instance, at the system level, a frontend Agent should be forcefully restricted to only reading global API definitions and only having write access to the frontend code directory, physically preventing it from "overstepping."
* **Contract Establishment:** Besides physical isolation, since Agents lack human "social consensus," we must use code to enforce it. For example:
    * **API-First Design:** The developer (or architect Entity) first defines a strict API contract (e.g., OpenAPI/Swagger specifications), data structures, or communication protocols.
    * **Mock and Stub Code Validation:** When the Agent develops within its isolated module, the system automatically provides a contract-based Mock environment. The Agent's output must pass contract-based automated testing (Validator mechanism) before initiating a Pull Request (PR).

#### 2. Relay-based Development
* **Pattern:** One developer runs with the Agent at a time, advancing deeply domain by domain. Once completed, they iteratively upgrade or hand off the project.
* **Characteristics:** Fully maximizes the Agent's functional breadth.
* **Limitations & Countermeasures:** Does not support parallel development, has a broad acceptance scope, and relies heavily on a single developer's global control. This pattern is **highly dependent on Context Management.** When a task is "relayed," the "design intent, compromises, and pending issues" of the current stage must be packed. This requires the Agent system to automatically generate and maintain a dynamic **"Context Snapshot"** outside the codebase to overcome "amnesia" caused by model input limits.
* **State Transfer:** In many architectures, context management relying solely on chat history and event streams is extremely inefficient. We need to introduce higher-dimensional state transfer mechanisms, such as **Structured Context Snapshots** and **Tiered Memory Retrieval**, allowing the taking-over Agent to quickly grasp the system's full picture and prerequisite intentions.

In actual engineering framework transformations, we often find that these two modes are not black and white. An advanced system architecture might adopt the Module pattern at a macro level to isolate different business domains, while utilizing the Relay pattern within a single complex micro-module, allowing the developer to lead the Agent in deep breakthroughs.

### Towards the Ideal State: Architecture Design Based on Entity Characteristics

From a more ideal perspective, we should no longer dwell on the physical distinction between "humans and Agents," but move towards **"defining the responsibility boundaries of Entities."**

An ideal software engineering scenario should allocate requirements, define phase boundaries, and route the entire development and acceptance workflow based on the characteristics of different Entities (whether human developers or AI Agents). To achieve this organic and efficient development environment, we need to implement the following three mechanisms in engineering:

1. **Introduce a "Planning & Orchestration Layer":** Execution-state Agents must never directly interface with vague requirements. Within the Entity collaboration network, there must be a node (which could be a Senior Developer or a dedicated Planning Agent) specifically responsible for "questioning requirements, breaking down tasks, and defining boundaries." It does not write code; it only translates macro requirements into sub-task protocols with strict **Acceptance Criteria** and **Blast Radius** definitions.
2. **Define a Standardized "Handshake Protocol":** Whether it's a handoff from "Human to Agent" or "Agent to Agent," a standardized information interface is required, rather than just a simple Prompt. The protocol must include:
    * **Goal:** The exact objective of the current step.
    * **Context:** Highly relevant dependency information (filtering out noise).
    * **Constraints:** Files that absolutely cannot be modified or principles that must be adhered to.
    * **Definition of Done (DoD):** Clear rules on how the Agent can self-determine task completion.
3. **Multi-modal Acceptance and Intermediate Feedback:** To cure the engineering disease of "no acceptance until the very end," future frameworks must **force the Agent to interrupt at key nodes and throw an "Approval Request"** (e.g., after generating a design doc, or before starting massive file modifications). This allows the human Entity to intervene at a low cost for directional verification.

Through continuous practical validation, the engineering community will inevitably explore a more inclusive and efficient new paradigm for human-machine development.