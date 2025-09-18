### Title: Beyond Black-Box Benchmarking: Observability, Analytics, and Optimization of Agentic Systems
#### Year: 2025
#### Authors: IBM Research (Dany Moshkovich, Hadar Mulian, Sergey Zeltyn, Natti Eder, Inna Skarbovsky, Roy Abitbol)


## goal
Propose a shift from classic outcome-only, “black-box” evaluation of agents to **behavioral benchmarking** backed by richer observability. Concretely: define taxonomies/semantics for logging agent behavior, show how to analyze those logs, and introduce a benchmark (ABBench) that evaluates *agent analytics systems* themselves—not just agents.

## state-of-the-art
- **Observability**: OpenTelemetry (OTel) and LLM-specific extensions (e.g., OpenLLMetry), plus lifecycle/debug tools like LangSmith, LangFuse, and LangTrace. These track tokens, cost, latency, traces/spans—mostly at the LLM call level.  
- **Benchmarking**: Typically black-box, reporting accuracy, time, or cost on final outcomes only.

## problems with state of the art
- **Flow variability**: identical inputs can trigger divergent execution graphs.  
- **Natural-language variability**: different phrasings or sampling cause different outcomes.  
- **Limited semantics**: OTel conventions don’t model agent entities (tasks, workflows, tools, orgs) or lifecycle events.  
- **Practitioner pain**: survey highlights major difficulty with nondeterministic flow, root-cause analysis, and insufficient analytics in current tools.

## experimental methodology
Two prongs:
1. **Empirical variability study**  
   - System: LangGraph “agentic calculator.”  
   - Setup: 50 prompts × 5 runs each.  
   - Metrics: Accuracy (MSE), execution-flow differences (graph-edit distance), cost (OpenAI-4o tokens → USD), latency.  
   - Analysis: variability summarized via coefficient of variation (CV).
2. **User study**  
   - 38 practitioners surveyed/interviewed to validate pain points and analytics needs.

## what tools did they use?
- **LangGraph** for agent execution.  
- **OpenAI-4o** for LLM calls.  
- **OpenTelemetry + proposed GenAI Events** for observability.  
- **IBM TAMAS** (Task-oriented Analytics for Multi-Agent Systems) as analytics case study.  
- **Open resources**: calculator repo + benchmark dataset.

## what benchmarks did they use?
- **ABBench (Agent Analytics Behavioral Benchmark)**  
  - 30 structured execution logs from the calculator system.  
  - Covers numeric + NL problems, parallel/distributed cases, syntax-error inputs.  
  - Each log includes ground-truth task-flow graph, summary metrics (tokens, time, LLM/tool calls), and labeled failures.  
  - Includes TAMAS outputs for comparison.

## is their test reproducible?
- **Yes (partially):**  
  - Open-source calculator + public ABBench logs and ground truth provided.  
  - Enables independent analytics testing and comparison.  
  - TAMAS is proprietary, but not required to use ABBench.  
  - Variability experiments reproducible with same model (OpenAI-4o).

## what was their proposed method?
- **Behavioral benchmarking**: evaluate agents (and analytics tools) by analyzing **runtime behavior** (task flows, decisions, issues).  
- **Taxonomies & semantics**: core entities (resources, tools, tasks, workflows, agents, orgs) and **GenAI Events** (create/start/end/fail, severities) bound to spans.  
- **Analytics taxonomy**:  
  - *Passive*: compute metrics, discover flows, derive recommendations.  
  - *Exploratory*: interactive troubleshooting, comparisons, retrieval-over-logs.  
  - *Interventional*: increase monitoring, inject tasks, swap components, tune configs.  
- **Benchmarking analytics systems**: compare outputs vs ground truth via graph-edit distance, table metric match rates, text similarity for failures.

## what were the results of the paper?
- **Variability study**  
  - *Math prompts*: mean CV ~63% (flow), ~43–46% (cost/latency/LLM calls).  
  - *NL prompts*: accuracy CV ~19%, similar variability in other metrics.  
  → Empirical evidence that agent runs are non-deterministic.  
- **User study (n=38)**  
  - ~80%: nondeterministic flow is a major issue.  
  - 66%: agents fail to follow instructions.  
  - 63%: poor tool selection.  
  - 60%: analytics insufficient.  
  - 77%: struggle with root cause.  
  - 76%: need for flow understanding is top priority.  
- **ABBench + TAMAS case study**  
  - TAMAS summary matched ground truth in ~60% of cases.  
  - Missed validator failures and struggled with syntax-error cases.  
  - Benchmark reveals concrete analytics gaps.

## extra notes
- **Optimization patterns**: decomposition, parallelization, merging → analytics-driven tradeoffs between quality, latency, cost.  
- **Evaluation unit**: shifts from *tasks* to **execution logs**, comparing flows with graph metrics.  
- **Future work**: extend taxonomies for long-horizon/multi-agent dynamics, expand datasets, improve real-time monitoring and adaptive optimization.
