# Awesome Agentic Evaluation

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![License: CC0 1.0](https://img.shields.io/badge/License-CC0_1.0-lightgrey.svg)](https://creativecommons.org/publicdomain/zero/1.0/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

> A curated list of benchmarks, environments, papers, competitions, and open-source platforms for evaluating AI agents in interactive, tool-using, dynamic, and production-like settings.

Agentic evaluation is different from plain LLM evaluation: agents plan, call tools, interact with users, change environment state, recover from failures, and operate across long horizons. This list focuses on interactive and environment-grounded evaluation, with an emphasis on open-source work from research labs, large companies, and startups.

## Contents

- [Scope](#scope)
- [Foundational Benchmark Lineages](#foundational-benchmark-lineages)
- [Competitions And Benchmark Platforms](#competitions-and-benchmark-platforms)
- [Benchmark Rigor And Methodology](#benchmark-rigor-and-methodology)
- [Benchmarks By Domain](#benchmarks-by-domain)
  - [Conversational And Tool-Using Agents](#conversational-and-tool-using-agents)
  - [Web And Computer-Use Agents](#web-and-computer-use-agents)
  - [Software Engineering And DevOps Agents](#software-engineering-and-devops-agents)
  - [Cybersecurity Agents](#cybersecurity-agents)
  - [Generalist And Real-World Work Benchmarks](#generalist-and-real-world-work-benchmarks)
  - [Dynamic And Evolving Environments](#dynamic-and-evolving-environments)
- [Open-Source Eval Tooling, Tracing, And Observability](#open-source-eval-tooling-tracing-and-observability)
- [Company-Backed Open Source](#company-backed-open-source)
- [Design Patterns For Agentic Evaluation](#design-patterns-for-agentic-evaluation)
- [Contributing](#contributing)
- [License](#license)

## Scope

This list prioritizes resources that evaluate one or more of the following:

- multi-turn interaction
- tool use and environment state changes
- user coordination, policy following, and safety constraints
- process quality, not just final answers
- reproducibility, realism, and benchmark validity
- production feedback loops through traces, logs, and regression testing

Generic static LLM benchmarks are out of scope unless they are directly useful for evaluating agents.

## Foundational Benchmark Lineages

### τ / tool-agent-user lineage

- [**τ-bench**](https://github.com/sierra-research/tau-bench) - Foundational benchmark for tool-agent-user interaction in real-world domains, introducing policy-aware conversational tasks and the `pass^k` reliability metric. The repository now notes that some original tasks are outdated and points readers toward newer τ-lineage work.
- [**τ-bench paper**](https://arxiv.org/abs/2406.12045) - Core paper that established the TAU framing for evaluating interactive customer-service-style agents.
- [**τ²-Bench**](https://github.com/sierra-research/tau2-bench) - Dual-control extension where the simulated user can also act on the shared environment through tools.
- [**τ²-Bench paper**](https://arxiv.org/abs/2506.07982) - Reference for coordination failures, user guidance, and shared-world interaction.

## Competitions And Benchmark Platforms

- [**AgentBeats**](https://github.com/agentbeats/agentbeats) - Open platform for agent evaluation where benchmarks themselves are packaged as assessor agents.
- [**AgentX-AgentBeats Competition**](https://rdi.berkeley.edu/agentx-agentbeats.html) - Competition focused on building benchmark agents and contestant agents for public-good evaluation tasks.
- [**agentify-example-tau-bench**](https://github.com/agentbeats/agentify-example-tau-bench) - Example of wrapping an existing benchmark into the AgentBeats ecosystem.
- [**AgentBeats Tutorials**](https://github.com/agentbeats/agentbeats_tutorials) - Starter material for integrating A2A-compatible agents and assessments.
- [**AgentBeats Info Session Deck**](https://rdi.berkeley.edu/assets/agentbeats-competition-info-session-deck.pdf) - Useful background on AAA, assessor agents, A2A and MCP standardization, registries, traces, and leaderboards.

## Benchmark Rigor And Methodology

- [**Establishing Best Practices for Building Rigorous Agentic Benchmarks**](https://arxiv.org/abs/2507.02825) - Introduces the Agentic Benchmark Checklist (ABC) and highlights common task-validity, outcome-validity, and reporting weaknesses in agent benchmarks.

Questions worth asking before trusting a benchmark:

- Is the task valid?
- Is the evaluator or reward signal valid?
- Can the setup be gamed?
- Are failure modes and caveats reported clearly?
- Does the benchmark reflect production reality?

## Benchmarks By Domain

### Conversational And Tool-Using Agents

- [**APIGen-MT**](https://arxiv.org/abs/2503.05779) - Multi-turn, tool-calling data and evaluation setup closely related to τ-style interaction.
- [**FlowBench**](https://arxiv.org/abs/2410.17126) - Evaluates tool-using agents with explicit workflow knowledge and planning structure.
- [**IntellAgent**](https://arxiv.org/abs/2502.14286) - Synthetic test-suite generation pipeline for policy-rich conversational agents, explicitly positioned as a proxy to τ-style evaluation.
- [**ToolSandbox**](https://arxiv.org/abs/2408.04682) - Stateful tool environment with fine-grained evaluation of partial progress and agent behavior.

### Web And Computer-Use Agents

- [**AgentLab**](https://github.com/ServiceNow/AgentLab) - Open-source framework for developing, testing, and benchmarking web agents at scale.
- [**BrowserGym**](https://github.com/ServiceNow/BrowserGym) - Gym-like environment ecosystem for browser-agent evaluation.
- [**OSWorld**](https://github.com/xlang-ai/osworld) - Multimodal benchmark for open-ended tasks in full computer environments.
- [**OSWorld-G**](https://github.com/xlang-ai/OSWorld-G) - Grounding-oriented extension in the OSWorld family.
- [**WebArena**](https://github.com/web-arena-x/webarena) - Realistic self-hostable web environment for autonomous web agents.
- [**WebArena-Verified**](https://github.com/ServiceNow/webarena-verified) - Verified release focused on more reproducible web-agent benchmarking.

### Software Engineering And DevOps Agents

- [**DevOps-Gym**](https://github.com/ucsb-mlsec/DevOps-Gym) - End-to-end benchmark for the DevOps lifecycle, including configuration, monitoring, issue resolution, and test generation.
- [**MLE-bench**](https://arxiv.org/abs/2410.07095) - Benchmark for machine-learning engineering and research workflows.
- [**MLGym**](https://github.com/facebookresearch/MLGym) - Gym-style environment and benchmark for machine-learning tasks.
- [**Multi-SWE-bench**](https://arxiv.org/abs/2504.02605) - Extends SWE-style evaluation to more diverse software issue settings.
- [**SWE-bench**](https://github.com/SWE-bench/SWE-bench) - Canonical benchmark for resolving real GitHub issues in real repositories.
- [**SWT-bench**](https://arxiv.org/abs/2406.12952) - Benchmark centered on regression-test generation for software agents.

### Cybersecurity Agents

- [**AutoAdvExBench**](https://arxiv.org/abs/2503.16486) - Benchmark family for adversarial exploit-generation settings.
- [**BountyBench**](https://arxiv.org/abs/2501.04572) - Cybersecurity benchmark oriented toward bug-bounty-style vulnerability discovery.
- [**CVE-Bench**](https://arxiv.org/abs/2502.08763) - Benchmark built around vulnerability analysis and CVE-centric tasks.
- [**CyberGym**](https://github.com/sunblaze-ucb/cybergym) - Large-scale execution-based benchmark for reproducing real-world vulnerabilities from real projects.
- [**Cybench**](https://arxiv.org/abs/2408.08926) - Cyber benchmark often cited in newer benchmark-comparison work.
- [**NYU CTF Bench**](https://arxiv.org/abs/2410.11428) - Capture-the-flag style benchmark for cybersecurity agents.

### Generalist And Real-World Work Benchmarks

- [**GAIA**](https://huggingface.co/gaia-benchmark) - Benchmark for general assistants that require reasoning, tool use, and real-world problem solving.
- [**GDPval**](https://arxiv.org/abs/2510.04374) - Benchmark for economically valuable expert work across occupations and sectors.
- [**GDPval in Inspect Evals**](https://github.com/UKGovernmentBEIS/inspect_evals) - Example of integrating GDPval into a broader open evaluation ecosystem.
- [**How Well Does Agent Development Reflect Real-World Work?**](https://arxiv.org/abs/2603.01203) - Perspective on how current agent development and benchmarks map to real work.

### Dynamic And Evolving Environments

- [**AUTOENV**](https://arxiv.org/abs/2505.22143) - Environment-generation direction for scalable and adaptive agent evaluation.
- [**The World Won't Stay Still: Programmable Evolution for Agent Benchmarks**](https://arxiv.org/abs/2603.05910) - Argues that static environments are insufficient and introduces programmable environment evolution.
- [**ToolQA-D**](https://arxiv.org/abs/2502.11508) - Dynamic evaluation setup for tool-use agents operating in changing conditions.

## Open-Source Eval Tooling, Tracing, And Observability

### Frameworks

- [**agentevals**](https://github.com/langchain-ai/agentevals) - Agent-focused evaluators with emphasis on trajectory and intermediate-step assessment.
- [**DeepEval**](https://github.com/confident-ai/deepeval) - Pytest-like framework for evaluating LLM systems, including multi-step workflows.
- [**Inspect Evals**](https://github.com/UKGovernmentBEIS/inspect_evals) - Large open collection of benchmark implementations and adapters.
- [**OpenAI Evals**](https://github.com/openai/evals) - General-purpose open-source eval framework and registry for LLM systems.
- [**OpenEvals**](https://github.com/langchain-ai/openevals) - Readymade evaluators for LLM applications and custom eval suites.

### Tracing, observability, and experiment management

- [**Braintrust Python SDK**](https://github.com/braintrustdata/braintrust-sdk-python) - SDK for logging, tracing, datasets, and evaluations.
- [**Braintrust TypeScript SDK**](https://github.com/braintrustdata/braintrust-sdk) - JavaScript and TypeScript side of the Braintrust tooling stack.
- [**Langfuse**](https://github.com/langfuse/langfuse) - Open-source LLM engineering platform for monitoring, evals, prompts, and debugging.
- [**Phoenix**](https://github.com/Arize-ai/phoenix) - Open-source observability platform for experimentation, evaluation, and troubleshooting.

## Company-Backed Open Source

### Research labs, large tech, and platform companies

- [**Arize / Phoenix**](https://github.com/Arize-ai/phoenix) - Company-backed observability and evaluation platform.
- [**Berkeley RDI / AgentBeats**](https://rdi.berkeley.edu/agentx-agentbeats.html) - Public benchmark-and-competition push for agentified evaluation.
- [**OpenAI / Evals**](https://github.com/openai/evals) - Foundational open-source eval framework for LLM systems.
- [**ServiceNow / AgentLab**](https://github.com/ServiceNow/AgentLab) - Reproducible development and test harness for web agents.
- [**ServiceNow / BrowserGym**](https://github.com/ServiceNow/BrowserGym) - Major open-source ecosystem for web-agent benchmarking.
- [**Sierra Research / τ lineage**](https://github.com/sierra-research) - Maintainers of the τ benchmark family for conversational and tool-using agents.

### Startups and OSS-native platforms

- [**Braintrust**](https://github.com/braintrustdata/braintrust-sdk-python) - Tracing and evaluation workflows for AI products.
- [**Confident AI / DeepEval**](https://github.com/confident-ai/deepeval) - OSS-first evaluation framework with heavy practitioner adoption.
- [**LangChain / agentevals**](https://github.com/langchain-ai/agentevals) - Trajectory-centric package specifically for agent evaluation.
- [**LangChain / OpenEvals**](https://github.com/langchain-ai/openevals) - General LLM evals package from a major agent framework ecosystem.
- [**Langfuse**](https://github.com/langfuse/langfuse) - Open-source observability and eval stack with a strong production focus.

## Design Patterns For Agentic Evaluation

A good benchmark usually makes its stance explicit on:

- outcome versus process scoring
- static versus dynamic environments
- single-control versus dual-control settings
- text judging versus execution-based judging
- end-state matching versus behavior or trajectory scoring
- offline benchmarking versus production feedback loops

## Contributing

Contributions are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## License

This work is licensed under [CC0-1.0](LICENSE).
