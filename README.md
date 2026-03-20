# Awesome Agentic Evaluation

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![License: CC0 1.0](https://img.shields.io/badge/License-CC0_1.0-lightgrey.svg)](https://creativecommons.org/publicdomain/zero/1.0/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

> A curated list of benchmarks, environments, papers, competitions, and open-source platforms for evaluating AI agents in interactive, tool-using, dynamic, and production-like settings.

Agentic evaluation is different from plain LLM evaluation: agents plan, call tools, interact with users, change environment state, recover from failures, and operate across long horizons. This list focuses on interactive and environment-grounded evaluation, with an emphasis on open-source work from research labs, large companies, and startups.

## Contents

- [Key Concepts And Glossary](#key-concepts-and-glossary)
- [Scope](#scope)
- [Foundational Benchmark Lineages](#foundational-benchmark-lineages)
- [Task Standards And Interoperability](#task-standards-and-interoperability)
- [Competitions And Benchmark Platforms](#competitions-and-benchmark-platforms)
- [Benchmark Rigor And Methodology](#benchmark-rigor-and-methodology)
- [Benchmarks By Domain](#benchmarks-by-domain)
  - [Conversational And Tool-Using Agents](#conversational-and-tool-using-agents)
  - [Web And Computer-Use Agents](#web-and-computer-use-agents)
  - [Software Engineering And DevOps Agents](#software-engineering-and-devops-agents)
  - [Cybersecurity Agents](#cybersecurity-agents)
  - [Safety And Dangerous-Capability Evaluation](#safety-and-dangerous-capability-evaluation)
  - [Multi-Agent Evaluation](#multi-agent-evaluation)
  - [Generalist And Real-World Work Benchmarks](#generalist-and-real-world-work-benchmarks)
  - [Dynamic And Evolving Environments](#dynamic-and-evolving-environments)
- [Holistic And Cross-Cutting Evaluation Frameworks](#holistic-and-cross-cutting-evaluation-frameworks)
- [Open-Source Eval Tooling, Tracing, And Observability](#open-source-eval-tooling-tracing-and-observability)
- [Company-Backed Open Source](#company-backed-open-source)
- [State-Of-The-Art Practices For Agentic Evaluation](#state-of-the-art-practices-for-agentic-evaluation)
- [Design Patterns For Agentic Evaluation](#design-patterns-for-agentic-evaluation)
- [Contributing](#contributing)
- [License](#license)

---

## Key Concepts And Glossary

This section defines core terms used throughout this guide. Whether you come from software engineering, ML research, product management, or policy, these definitions should help you navigate the landscape.

| Term | Definition |
| --- | --- |
| **Agent** | An AI system that perceives its environment, makes decisions, and takes actions autonomously over multiple steps to achieve a goal. Unlike a simple chatbot that answers one question at a time, an agent maintains state, plans ahead, and adapts its behavior based on feedback. |
| **Agentic Evaluation** | The practice of measuring how well an AI agent performs in realistic, interactive settings — not just whether it gets the right answer, but whether it follows the right process, uses tools correctly, recovers from errors, and respects constraints. |
| **Benchmark** | A standardized test suite with predefined tasks, inputs, expected outputs, and scoring criteria. Benchmarks enable reproducible comparison of different agents or models under controlled conditions. |
| **Tool Use / Function Calling** | The ability of an agent to invoke external functions, APIs, or services (e.g., search engines, databases, code interpreters) as part of its reasoning process. This is a core capability that separates agents from plain language models. |
| **Multi-Turn Interaction** | A conversation or task that unfolds over multiple exchanges between the agent and a user, environment, or other agent. Each turn can change the state and influence subsequent decisions. |
| **Trajectory** | The complete sequence of actions, observations, and decisions an agent makes while attempting a task. Trajectory evaluation judges the quality of the *process*, not just the final outcome. |
| **pass^k** | A reliability metric introduced by τ-bench. Instead of measuring whether an agent *can* succeed (like pass@k which checks if any of k attempts succeeds), pass^k measures whether an agent succeeds on *all* k independent attempts — testing consistency and reliability. |
| **Outcome vs. Process Scoring** | *Outcome scoring* checks only the final result (did the agent produce the correct answer?). *Process scoring* evaluates intermediate steps (did the agent use the right tools? Did it follow the correct policy? Did it ask clarifying questions when appropriate?). |
| **Static vs. Dynamic Environments** | A *static* environment stays the same across evaluations. A *dynamic* environment changes — new data arrives, APIs break, conditions shift — forcing the agent to adapt. Dynamic environments better reflect production reality. |
| **Single-Control vs. Dual-Control** | In *single-control* settings, only the agent acts on the environment. In *dual-control* settings (like τ²-bench), both the agent and the simulated user can change the shared environment, introducing coordination challenges. |
| **Execution-Based Judging** | Evaluating the agent by actually running its outputs (e.g., executing generated code, applying a patch, running a command) rather than using pattern matching or LLM-based text comparison. This provides stronger correctness guarantees. |
| **LLM-as-Judge** | Using another language model to evaluate an agent's outputs. While scalable, this approach can introduce biases (verbosity bias, self-preference) and requires careful calibration. |
| **Assessor Agent** | An AI agent specifically designed to evaluate other agents (as in the AgentBeats framework). This enables scalable, automated evaluation using agent-to-agent protocols. |
| **Sandbox / Gym Environment** | An isolated, controlled environment (often containerized) where agents can be safely tested. Inspired by OpenAI Gym, these environments provide standardized observation and action interfaces. |
| **Contamination** | When a model has seen benchmark data during training, inflating its scores beyond its true capability. A major validity concern for all benchmarks, especially static ones. |

## Scope

This list prioritizes resources that evaluate one or more of the following:

- **Multi-turn interaction** — agents that maintain context and act across multiple conversational exchanges
- **Tool use and environment state changes** — agents that call APIs, execute code, modify files, or interact with external systems
- **User coordination, policy following, and safety constraints** — agents that must respect rules, ask clarifying questions, and coordinate with humans
- **Process quality, not just final answers** — evaluating *how* an agent solves a problem, including intermediate reasoning and decision-making
- **Reproducibility, realism, and benchmark validity** — benchmarks that resist gaming, reflect real-world conditions, and produce consistent results
- **Production feedback loops** — using traces, logs, and regression testing to continuously evaluate deployed agents

Generic static LLM benchmarks are out of scope unless they are directly useful for evaluating agents.

## Foundational Benchmark Lineages

These are benchmark families that have shaped how the community thinks about agent evaluation. Understanding their design choices helps contextualize newer work.

### τ / tool-agent-user lineage (Sierra Research)

The τ (tau) family focuses on the triangle of interactions between a **t**ool, an **a**gent, and a **u**ser. These benchmarks model realistic customer-service-style scenarios where an agent must follow business policies while helping a simulated user accomplish tasks via tool calls.

- [**τ-bench**](https://github.com/sierra-research/tau-bench) — Foundational benchmark for tool-agent-user interaction in real-world domains (airline, retail). Introduces policy-aware conversational tasks and the `pass^k` reliability metric that measures consistency across repeated attempts, not just best-case performance. The repository notes that some original tasks are outdated and points readers toward newer τ-lineage work.
- [**τ-bench paper**](https://arxiv.org/abs/2406.12045) — Core paper that established the TAU framing for evaluating interactive customer-service-style agents.
- [**τ²-Bench**](https://github.com/sierra-research/tau2-bench) — Dual-control extension where both the agent and the simulated user can act on the shared environment through tools. This surfaces coordination failures that single-control setups miss entirely — e.g., the user booking a flight while the agent is simultaneously trying to modify the same reservation.
- [**τ²-Bench paper**](https://arxiv.org/abs/2506.07982) — Reference for coordination failures, user guidance, and shared-world interaction.

### SWE-bench lineage (Princeton NLP → Stanford)

The SWE-bench family evaluates whether agents can resolve real software engineering tasks drawn from actual GitHub issue-PR pairs. It has become the canonical benchmark for coding agents and has spawned multiple extensions.

- [**SWE-bench**](https://github.com/SWE-bench/SWE-bench) — Canonical benchmark: given a repository and a GitHub issue description, generate a patch that resolves the problem. Tasks are drawn from real open-source projects (Django, scikit-learn, sympy, etc.) with real test suites for execution-based verification. Originally developed at Princeton, now maintained across Princeton and Stanford. ICLR 2024 Oral.
- [**SWE-bench Verified**](https://openai.com/index/introducing-swe-bench-verified/) — A curated subset of 500 problems verified by professional software engineers as solvable and unambiguous. Developed in collaboration with OpenAI Preparedness.
- [**SWE-bench Multimodal**](https://arxiv.org/abs/2410.03859) — Extension to visual software domains (UI bugs, plot rendering issues), testing whether agents can generalize beyond text-only code. ICLR 2025.
- [**SWE-agent**](https://github.com/SWE-agent/SWE-agent) — Reference agent implementation that established strong baselines on SWE-bench and demonstrated the importance of agent-computer interface design.
- [**SWE-smith**](https://github.com/SWE-bench/SWE-smith) — Dedicated toolkit for creating synthetic SWE training data at scale, addressing data scarcity for training coding agents.

### AgentBench lineage (Tsinghua University)

- [**AgentBench**](https://github.com/THUDM/AgentBench) — Multi-dimensional benchmark across 8 distinct environments (operating system, database, knowledge graph, card game, lateral thinking, web shopping, web browsing, house-holding) for evaluating LLM-as-Agent reasoning and decision-making. Identified that poor long-term reasoning, decision-making, and instruction following are the main bottlenecks. ICLR 2024. ([paper](https://arxiv.org/abs/2308.03688))

## Task Standards And Interoperability

Task standards define a common format for writing evaluation tasks so that different organizations can share and reuse each other's work without reimplementing everything from scratch. This is critical because building high-quality tasks is expensive.

- [**METR Task Standard**](https://github.com/METR/task-standard) — A common format for defining AI agent evaluation tasks, developed by [METR](https://metr.org/) (formerly ARC Evals), a non-profit focused on evaluating frontier model autonomy. Tasks specify an environment (container/VM), instructions, and a scoring function. Already used to define 200+ task families across AI R&D, cybersecurity, and general autonomy. Includes adaptors for running existing benchmarks (GAIA, AgentBench, SWE-bench, picoCTF, HumanEval, GPQA) in the METR format.
- [**A2A (Agent-to-Agent Protocol)**](https://github.com/google/A2A) — Google's open protocol for inter-agent communication. Relevant to evaluation because it enables standardized assessor-agent interactions (as used in the AgentBeats framework).
- [**MCP (Model Context Protocol)**](https://modelcontextprotocol.io/) — Anthropic's open protocol for connecting AI models to data sources and tools. Provides a standard interface for tool-use evaluation.

## Competitions And Benchmark Platforms

Competitions drive the field forward by providing standardized leaderboards, incentivizing reproducibility, and surfacing novel evaluation methodologies.

- [**AgentBeats**](https://github.com/agentbeats/agentbeats) — Open platform for agent evaluation where benchmarks themselves are packaged as *assessor agents*. This "agents evaluating agents" model scales evaluation beyond manually curated test suites.
- [**AgentX-AgentBeats Competition**](https://rdi.berkeley.edu/agentx-agentbeats.html) — UC Berkeley RDI-hosted competition focused on building benchmark agents and contestant agents for public-good evaluation tasks. Demonstrates the AAA (Agent, Assessor, Arena) paradigm.
- [**agentify-example-tau-bench**](https://github.com/agentbeats/agentify-example-tau-bench) — Example of wrapping an existing benchmark (τ-bench) into the AgentBeats ecosystem, illustrating how to convert traditional benchmarks into assessor-agent format.
- [**AgentBeats Tutorials**](https://github.com/agentbeats/agentbeats_tutorials) — Starter material for integrating A2A-compatible agents and assessments.
- [**AgentBeats Info Session Deck**](https://rdi.berkeley.edu/assets/agentbeats-competition-info-session-deck.pdf) — Background on the AAA framework, assessor agents, A2A and MCP standardization, registries, traces, and leaderboards.
- [**Berkeley Function Calling Leaderboard (BFCL)**](https://gorilla.cs.berkeley.edu/leaderboard.html) — UC Berkeley's live leaderboard for evaluating function-calling capabilities of LLMs. Covers simple, parallel, multiple, and multi-turn function calling across Python, Java, JavaScript, and REST APIs. V4 adds agentic web search, memory management, and format sensitivity tests. ([GitHub](https://github.com/ShishirPatil/gorilla/tree/main/berkeley-function-call-leaderboard), [paper](https://arxiv.org/abs/2403.15813))

## Benchmark Rigor And Methodology

A benchmark is only useful if its results are trustworthy. This section covers work on understanding and improving the quality of agent benchmarks themselves — meta-evaluation, if you will.

- [**Establishing Best Practices for Building Rigorous Agentic Benchmarks**](https://arxiv.org/abs/2507.02825) — Introduces the Agentic Benchmark Checklist (ABC) and highlights common task-validity, outcome-validity, and reporting weaknesses in agent benchmarks. Essential reading before publishing or trusting any benchmark.

**Questions worth asking before trusting a benchmark:**

| Dimension | What to check |
| --- | --- |
| **Task Validity** | Are tasks representative of real work? Are instructions unambiguous? Could a competent human solve them? |
| **Evaluator Validity** | Does the scoring function actually measure what it claims? Are there false positives or false negatives? |
| **Gaming Resistance** | Can the benchmark be solved by shortcuts (e.g., memorization, pattern matching) that don't reflect genuine capability? |
| **Failure Transparency** | Are failure modes, edge cases, and caveats reported clearly? |
| **Production Relevance** | Does benchmark performance predict real-world deployment quality? |
| **Contamination Risk** | Could models have seen the test data during training? Are there mechanisms to detect or prevent this? |
| **Reproducibility** | Can results be independently reproduced? Are environments, prompts, and scoring deterministic? |

## Benchmarks By Domain

### Conversational And Tool-Using Agents

These benchmarks test an agent's ability to carry on multi-turn conversations, decide when and how to call tools, handle ambiguous requests, and follow policies or constraints.

- [**APIGen-MT**](https://arxiv.org/abs/2503.05779) — Multi-turn, tool-calling data and evaluation setup closely related to τ-style interaction. Generates verifiable multi-turn API-calling trajectories.
- [**FlowBench**](https://arxiv.org/abs/2410.17126) — Evaluates tool-using agents on tasks with explicit workflow knowledge and planning structure. Tests whether agents can follow prescribed workflows rather than ad-hoc reasoning.
- [**Gorilla / Berkeley Function Calling**](https://gorilla.cs.berkeley.edu/) — UC Berkeley's Gorilla project includes both a function-calling LLM and the BFCL evaluation suite. The leaderboard tracks LLM ability to generate correct API calls across languages and complexity levels. ([GitHub](https://github.com/ShishirPatil/gorilla))
- [**IntellAgent**](https://arxiv.org/abs/2502.14286) — Synthetic test-suite generation pipeline for policy-rich conversational agents. Automatically generates evaluation scenarios from policy documents, explicitly positioned as a scalable proxy to τ-style evaluation.
- [**ToolSandbox**](https://arxiv.org/abs/2408.04682) — Stateful tool environment with fine-grained evaluation of partial progress and agent behavior. Measures not just final success but intermediate tool-use quality.

### Web And Computer-Use Agents

These benchmarks evaluate agents that interact with real web browsers, operating systems, or graphical interfaces. They test perception (understanding screen content), planning (deciding what to click/type), and execution (performing actions correctly).

- [**AgentLab**](https://github.com/ServiceNow/AgentLab) — Open-source framework for developing, testing, and benchmarking web agents at scale. Provides reproducible evaluation harnesses across multiple web benchmarks.
- [**BrowserGym**](https://github.com/ServiceNow/BrowserGym) — Gym-like environment ecosystem for browser-agent evaluation. Defines a standardized observation-action space for web agents, enabling apples-to-apples comparison.
- [**OSWorld**](https://github.com/xlang-ai/osworld) — Multimodal benchmark for open-ended tasks in full computer environments (Ubuntu desktop). Tests agents on real-world tasks like spreadsheet editing, web browsing, and system administration using screenshot and accessibility-tree observations.
- [**OSWorld-G**](https://github.com/xlang-ai/OSWorld-G) — Grounding-oriented extension in the OSWorld family, focusing on fine-grained visual grounding in desktop environments.
- [**WebArena**](https://github.com/web-arena-x/webarena) — Realistic, self-hostable web environment with functional websites (e-shopping, forums, maps, content management) for testing autonomous web agents on complex, multi-step tasks. Carnegie Mellon University. ([paper](https://arxiv.org/abs/2307.13854))
- [**WebArena-Verified**](https://github.com/ServiceNow/webarena-verified) — Verified release focused on more reproducible web-agent benchmarking through human-validated task correctness.
- [**VisualWebArena**](https://github.com/web-arena-x/visualwebarena) — Extension of WebArena adding visually grounded tasks that require understanding images, screenshots, and visual layouts on the web. ([paper](https://arxiv.org/abs/2401.13649))

### Software Engineering And DevOps Agents

These benchmarks test agents on real-world software engineering workflows: fixing bugs, writing tests, debugging CI failures, configuring environments, and conducting ML experiments. They typically use execution-based evaluation (running test suites) for reliable scoring.

- [**DevOps-Gym**](https://github.com/ucsb-mlsec/DevOps-Gym) — End-to-end benchmark for the DevOps lifecycle, including configuration, monitoring, issue resolution, and test generation. UC Santa Barbara.
- [**MLE-bench**](https://arxiv.org/abs/2410.07095) — Benchmark for machine-learning engineering and research workflows based on Kaggle competitions. Tests whether agents can perform data analysis, feature engineering, model training, and submission preparation. OpenAI.
- [**MLGym**](https://github.com/facebookresearch/MLGym) — Meta's gym-style environment and benchmark for machine-learning research tasks. Provides standardized environments for testing ML research agents.
- [**Multi-SWE-bench**](https://arxiv.org/abs/2504.02605) — Extends SWE-style evaluation to multi-language settings (Java, TypeScript, Rust, Go) and more diverse software issues.
- [**SWE-bench**](https://github.com/SWE-bench/SWE-bench) — The canonical benchmark for resolving real GitHub issues in real repositories. Uses Docker-based evaluation for reproducibility. Princeton NLP / Stanford. See [Foundational Benchmark Lineages](#foundational-benchmark-lineages) for full details.
- [**SWT-bench**](https://arxiv.org/abs/2406.12952) — Benchmark centered on regression-test generation for software agents. Tests whether agents can write tests that catch bugs, rather than fixing them.

### Cybersecurity Agents

These benchmarks evaluate agents on security-relevant tasks: finding vulnerabilities, exploiting bugs, solving capture-the-flag challenges, and performing security analysis. They require agents to reason about code, systems, and adversarial scenarios.

- [**AutoAdvExBench**](https://arxiv.org/abs/2503.16486) — Benchmark family for adversarial exploit-generation settings.
- [**BountyBench**](https://arxiv.org/abs/2501.04572) — Cybersecurity benchmark oriented toward bug-bounty-style vulnerability discovery in real software.
- [**CVE-Bench**](https://arxiv.org/abs/2502.08763) — Benchmark built around vulnerability analysis and CVE-centric tasks, testing whether agents can analyze and reproduce known vulnerabilities.
- [**CyberGym**](https://github.com/sunblaze-ucb/cybergym) — Large-scale execution-based benchmark for reproducing real-world vulnerabilities from real projects. UC Berkeley.
- [**Cybench**](https://arxiv.org/abs/2408.08926) — Comprehensive cyber benchmark covering multiple CTF categories, often cited in newer benchmark-comparison work.
- [**NYU CTF Bench**](https://arxiv.org/abs/2410.11428) — Capture-the-flag style benchmark for cybersecurity agents. NYU.

### Safety And Dangerous-Capability Evaluation

These benchmarks assess whether AI agents pose risks through autonomous capabilities like self-replication, deception, resource acquisition, or resisting oversight. Critical for frontier model evaluation and policy decisions.

- [**Dangerous Capability Evaluations**](https://github.com/google-deepmind/dangerous-capability-evaluations) — Google DeepMind's evaluation suite covering in-house CTF challenges, self-proliferation tasks, and self-reasoning tasks. Tests whether frontier models can autonomously replicate, acquire resources, or reason about their own situation. ([paper](https://arxiv.org/abs/2403.13793))
- [**Anthropic Model-Written Evaluations**](https://github.com/anthropics/evals) — Datasets testing models for persona consistency, sycophancy, advanced AI risks (power-seeking, self-preservation), and bias. Demonstrates the approach of using language models to generate evaluation data at scale. ([paper](https://arxiv.org/abs/2212.09251))
- [**HELM Safety Leaderboard**](https://crfm.stanford.edu/helm/safety/latest/) — Stanford CRFM's aggregated safety benchmark covering 6 risk vectors across popular safety benchmarks. Part of the broader HELM framework.
- [**AIR-Bench**](https://crfm.stanford.edu/helm/air-bench/latest/) — Safety benchmark based on emerging government regulations and company policies, evaluating model compliance with real-world safety requirements. Stanford CRFM.

### Multi-Agent Evaluation

Multi-agent evaluation is an emerging area that tests how agents collaborate, coordinate, or compete with each other. This is increasingly important as deployed systems involve multiple cooperating agents.

- [**AutoGen Bench**](https://github.com/microsoft/autogen/tree/main/python/packages/agbench) — Benchmarking suite integrated within Microsoft's AutoGen multi-agent framework. Evaluates multi-agent orchestration performance on standard tasks.
- [**Magentic-One**](https://github.com/microsoft/autogen/tree/main/python/packages/magentic-one-cli) — Microsoft's state-of-the-art multi-agent team built on AutoGen. Handles web browsing, code execution, and file handling tasks, serving as both a reference agent implementation and an evaluation subject. ([paper](https://arxiv.org/abs/2411.04468))
- [**AgentBench**](https://github.com/THUDM/AgentBench) — While primarily a single-agent benchmark, its 8 diverse environments serve as testbeds for multi-agent architectures. See [Foundational Benchmark Lineages](#foundational-benchmark-lineages).

### Generalist And Real-World Work Benchmarks

These benchmarks aim to measure agent capabilities on tasks that resemble actual human work — information gathering, analysis, multi-step problem solving, and professional expertise.

- [**GAIA**](https://huggingface.co/gaia-benchmark) — Benchmark for general AI assistants that require multi-step reasoning, tool use, and real-world problem solving. Tasks are designed so that a human can verify the answer easily but solving requires genuine capability. Meta / HuggingFace.
- [**GDPval**](https://arxiv.org/abs/2510.04374) — Benchmark for economically valuable expert work across occupations and sectors. Maps agent capabilities to real economic value.
- [**GDPval in Inspect Evals**](https://github.com/UKGovernmentBEIS/inspect_evals) — Example of integrating GDPval into the UK AI Safety Institute's broader open evaluation ecosystem.
- [**How Well Does Agent Development Reflect Real-World Work?**](https://arxiv.org/abs/2603.01203) — Critical perspective on how current agent development and benchmarks map to actual real-world work practices and requirements.
- [**GoEX (Gorilla Execution Engine)**](https://github.com/ShishirPatil/gorilla/tree/main/goex) — UC Berkeley's runtime for executing LLM-generated actions (API calls, code) with "post-facto validation," "undo," and "damage confinement" abstractions. Directly relevant to evaluating agents in production where actions have real consequences. ([paper](https://arxiv.org/abs/2404.06921))

### Dynamic And Evolving Environments

Static benchmarks eventually saturate and become gameable. These resources address the fundamental challenge of creating evaluation environments that change over time, preventing memorization and testing true adaptation.

- [**AUTOENV**](https://arxiv.org/abs/2505.22143) — Environment-generation direction for scalable and adaptive agent evaluation. Automatically generates new evaluation environments rather than relying on hand-crafted ones.
- [**The World Won't Stay Still: Programmable Evolution for Agent Benchmarks**](https://arxiv.org/abs/2603.05910) — Argues that static environments are insufficient and introduces programmable environment evolution, where the benchmark itself can change its tasks and conditions over time.
- [**ToolQA-D**](https://arxiv.org/abs/2502.11508) — Dynamic evaluation setup for tool-use agents operating in changing conditions. Tests whether agents can adapt when the data behind their tools changes.

## Holistic And Cross-Cutting Evaluation Frameworks

These frameworks aim to evaluate models and agents across multiple dimensions simultaneously — capabilities, safety, fairness, robustness, and efficiency — rather than focusing on a single benchmark.

- [**HELM (Holistic Evaluation of Language Models)**](https://github.com/stanford-crfm/helm) — Stanford CRFM's comprehensive open-source framework for evaluating foundation models across many dimensions: accuracy, calibration, robustness, fairness, bias, toxicity, and efficiency. Maintains [multiple leaderboards](https://crfm.stanford.edu/helm/) covering capabilities, safety, vision-language (VHELM), text-to-image (HEIM), medical (MedHELM), financial, multilingual, and domain-specific evaluations. The gold standard for holistic model evaluation. ([paper](https://openreview.net/forum?id=iO4LZibEqW))
- [**Inspect Evals**](https://github.com/UKGovernmentBEIS/inspect_evals) — Large open collection of benchmark implementations maintained by the UK AI Safety Institute. Provides a unified framework for running many different benchmarks with consistent tooling, including GAIA, GDPval, and others.

## Open-Source Eval Tooling, Tracing, And Observability

### Frameworks

These tools help you build, run, and manage evaluations. They provide the infrastructure for defining test cases, running agents against them, and collecting results.

- [**agentevals**](https://github.com/langchain-ai/agentevals) — Agent-focused evaluators with emphasis on trajectory and intermediate-step assessment. Supports both reference-based and reference-free evaluation of agent behavior.
- [**DeepEval**](https://github.com/confident-ai/deepeval) — Pytest-like framework for evaluating LLM systems, including multi-step workflows. Provides built-in metrics (faithfulness, answer relevancy, hallucination) and integrates with CI/CD pipelines.
- [**Inspect Evals**](https://github.com/UKGovernmentBEIS/inspect_evals) — Large open collection of benchmark implementations and adapters maintained by the UK AI Safety Institute. Designed for reproducible evaluations with standardized tooling.
- [**OpenAI Evals**](https://github.com/openai/evals) — General-purpose open-source eval framework and registry for LLM systems. Established many of the conventions now used across the ecosystem.
- [**OpenEvals**](https://github.com/langchain-ai/openevals) — Readymade evaluators for LLM applications and custom eval suites. Covers common evaluation patterns (correctness, style, safety) out of the box.

### Tracing, Observability, And Experiment Management

Production agent evaluation requires visibility into what agents are actually doing. These tools provide logging, tracing, and analysis capabilities for understanding agent behavior at scale.

- [**Braintrust Python SDK**](https://github.com/braintrustdata/braintrust-sdk-python) — SDK for logging, tracing, datasets, and evaluations. Supports A/B testing of prompts and model configurations.
- [**Braintrust TypeScript SDK**](https://github.com/braintrustdata/braintrust-sdk) — JavaScript and TypeScript side of the Braintrust tooling stack.
- [**Langfuse**](https://github.com/langfuse/langfuse) — Open-source LLM engineering platform for monitoring, evals, prompts, and debugging. Provides production-grade tracing with cost tracking and latency analysis.
- [**Phoenix**](https://github.com/Arize-ai/phoenix) — Open-source observability platform for experimentation, evaluation, and troubleshooting. Strong support for embedding visualization and drift detection.

## Company-Backed Open Source

Understanding who backs which projects helps you gauge long-term maintenance commitments, potential biases, and ecosystem compatibility.

### Research Labs And Universities

| Organization | Key Contributions |
| --- | --- |
| **UC Berkeley RDI** | [AgentBeats](https://rdi.berkeley.edu/agentx-agentbeats.html) competition/platform, [CyberGym](https://github.com/sunblaze-ucb/cybergym) security benchmark |
| **UC Berkeley (Gorilla/BFCL)** | [Berkeley Function Calling Leaderboard](https://gorilla.cs.berkeley.edu/leaderboard.html), [Gorilla](https://github.com/ShishirPatil/gorilla) LLM + tools, [GoEX](https://github.com/ShishirPatil/gorilla/tree/main/goex) execution engine |
| **Stanford CRFM** | [HELM](https://github.com/stanford-crfm/helm) holistic evaluation framework, [Safety](https://crfm.stanford.edu/helm/safety/latest/) / [Capabilities](https://crfm.stanford.edu/helm/capabilities/latest/) / [VHELM](https://crfm.stanford.edu/helm/vhelm/latest/) leaderboards, [AIR-Bench](https://crfm.stanford.edu/helm/air-bench/latest/) |
| **Princeton NLP** | [SWE-bench](https://github.com/SWE-bench/SWE-bench) family (canonical coding-agent benchmark), [SWE-agent](https://github.com/SWE-agent/SWE-agent) |
| **METR** | [Task Standard](https://github.com/METR/task-standard) for autonomous capability evaluation, frontier model safety assessments |
| **Tsinghua University** | [AgentBench](https://github.com/THUDM/AgentBench) multi-dimensional agent evaluation |
| **Carnegie Mellon** | [WebArena](https://github.com/web-arena-x/webarena) and [VisualWebArena](https://github.com/web-arena-x/visualwebarena) web-agent benchmarks |

### Large Tech And Platform Companies

| Organization | Key Contributions |
| --- | --- |
| **OpenAI** | [Evals](https://github.com/openai/evals) framework, [SWE-bench Verified](https://openai.com/index/introducing-swe-bench-verified/) collaboration, [MLE-bench](https://arxiv.org/abs/2410.07095) |
| **Google DeepMind** | [Dangerous Capability Evaluations](https://github.com/google-deepmind/dangerous-capability-evaluations), frontier safety assessment |
| **Google** | [A2A (Agent-to-Agent Protocol)](https://github.com/google/A2A) for standardized agent communication |
| **Anthropic** | [Model-Written Evaluations](https://github.com/anthropics/evals), [MCP (Model Context Protocol)](https://modelcontextprotocol.io/), safety-focused evaluation research |
| **Microsoft** | [AutoGen](https://github.com/microsoft/autogen) multi-agent framework with [AutoGen Bench](https://github.com/microsoft/autogen/tree/main/python/packages/agbench), [Magentic-One](https://github.com/microsoft/autogen/tree/main/python/packages/magentic-one-cli) |
| **Meta** | [MLGym](https://github.com/facebookresearch/MLGym) ML research agent benchmark, [GAIA](https://huggingface.co/gaia-benchmark) co-development |
| **ServiceNow** | [AgentLab](https://github.com/ServiceNow/AgentLab), [BrowserGym](https://github.com/ServiceNow/BrowserGym), [WebArena-Verified](https://github.com/ServiceNow/webarena-verified) |
| **Sierra Research** | [τ-bench](https://github.com/sierra-research/tau-bench) / [τ²-bench](https://github.com/sierra-research/tau2-bench) benchmark family |
| **UK AI Safety Institute** | [Inspect Evals](https://github.com/UKGovernmentBEIS/inspect_evals) evaluation platform |

### Startups And OSS-Native Platforms

| Organization | Key Contributions |
| --- | --- |
| **Arize AI** | [Phoenix](https://github.com/Arize-ai/phoenix) observability and evaluation platform |
| **Braintrust** | [Braintrust SDK](https://github.com/braintrustdata/braintrust-sdk-python) for tracing and evaluation workflows |
| **Confident AI** | [DeepEval](https://github.com/confident-ai/deepeval) OSS-first evaluation framework |
| **LangChain** | [agentevals](https://github.com/langchain-ai/agentevals) trajectory evaluation, [OpenEvals](https://github.com/langchain-ai/openevals) general LLM evals |
| **Langfuse** | [Langfuse](https://github.com/langfuse/langfuse) open-source observability and eval stack |

## State-Of-The-Art Practices For Agentic Evaluation

Drawing on lessons from the benchmarks, papers, and competitions listed above, the following emerging practices represent the current state of the art for evaluating AI agents.

### 1. Use Execution-Based Evaluation Over Text Matching

**Why:** Text-based comparison (exact match, BLEU, LLM-as-judge) is brittle and unreliable for agentic tasks. An agent might produce a correct database query with different formatting, or fix a bug with a different but equally valid approach.

**Best practice:** Run the agent's outputs in a real environment — execute the code, apply the patch, run the test suite, check the database state. SWE-bench, CyberGym, and DevOps-Gym all use this approach. If execution-based evaluation isn't possible, combine multiple evaluation signals rather than relying on a single LLM judge.

### 2. Measure Reliability, Not Just Peak Performance

**Why:** An agent that succeeds 1 out of 10 times on a task is useless in production but might rank well on pass@10 leaderboards. Real users need consistency.

**Best practice:** Use metrics like `pass^k` (introduced by τ-bench) that penalize inconsistency. Report variance across runs. Test with different seeds and prompt formulations. The BFCL leaderboard reports results across multiple API call formats and complexity levels.

### 3. Evaluate Process, Not Just Outcomes

**Why:** A correct final answer can mask dangerous intermediate behavior — the agent might have hallucinated tool calls that happened to produce the right result, or violated safety policies along the way.

**Best practice:** Score agent trajectories and intermediate steps. Check policy compliance at each turn (as τ-bench does). Use tools like `agentevals` that support trajectory-level evaluation. Log every tool call, observation, and decision point for post-hoc analysis.

### 4. Design For Contamination Resistance

**Why:** Static benchmarks inevitably leak into training data. Models that have memorized answers appear capable but aren't.

**Best practice:** Use dynamic environments (AUTOENV, ToolQA-D) that generate fresh tasks. Maintain hidden test sets with private evaluation (as SWE-bench Multimodal does). Regularly refresh benchmark data. Consider "live" evaluation with continuously updated data (as BFCL V2 does with enterprise-contributed real-world scenarios).

### 5. Test Dual-Control And Coordination

**Why:** Most real agent deployments involve coordination with humans or other agents who can independently change the environment. Single-agent benchmarks miss coordination failures entirely.

**Best practice:** Use dual-control benchmarks like τ²-bench where both user and agent can act. Test multi-agent scenarios where agents must share resources or information. Evaluate how agents handle conflicting actions or stale state.

### 6. Sandbox Everything, Log Everything

**Why:** Agents that execute code, call APIs, or modify files can cause real damage. Evaluation infrastructure must be isolated, and every action must be traceable.

**Best practice:** Use containerized environments (Docker, as SWE-bench does). Implement "undo" and "damage confinement" abstractions (as GoEX does). Deploy comprehensive tracing (Langfuse, Phoenix, Braintrust) for every evaluation run. The METR Task Standard provides a template for isolated evaluation environments.

### 7. Validate Your Benchmark Before Publishing

**Why:** Many published benchmarks contain ambiguous tasks, incorrect ground truth, or evaluation functions that don't measure what they claim to measure.

**Best practice:** Follow the ABC (Agentic Benchmark Checklist). Have human experts verify task solvability (as SWE-bench Verified did with professional engineers). Test your evaluation function against known-correct and known-incorrect solutions. Report inter-annotator agreement. Be transparent about failure modes and limitations.

## Design Patterns For Agentic Evaluation

When designing or choosing a benchmark, understanding these fundamental design axes helps clarify what is being measured and what is being missed. A good benchmark makes its stance explicit on each.

| Design Axis | Options | Trade-offs |
| --- | --- | --- |
| **Scoring Target** | Outcome vs. Process | Outcome is simpler but misses how the agent got there; process is richer but harder to define and score |
| **Environment Dynamics** | Static vs. Dynamic | Static is reproducible but gameable; dynamic resists memorization but is harder to maintain |
| **Control Model** | Single-control vs. Dual/Multi-control | Single is simpler; dual/multi better reflects real coordination challenges |
| **Judging Method** | Text matching vs. LLM judge vs. Execution-based | Execution is strongest but requires sandboxing; LLM judge scales but introduces bias; text matching is too brittle for open-ended tasks |
| **Scoring Granularity** | End-state matching vs. Trajectory scoring | End-state is pass/fail; trajectory reveals partial progress and failing patterns |
| **Evaluation Mode** | Offline benchmarking vs. Production feedback loops | Offline is controlled; production feedback captures real deployment challenges but is harder to standardize |
| **Task Source** | Hand-crafted vs. Programmatic vs. Real-world collected | Hand-crafted is high quality but expensive; programmatic scales but may lack realism; real-world (like SWE-bench) is authentic but noisy |

## Contributing

Contributions are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## License

This work is licensed under [CC0-1.0](LICENSE).
