# CausalProbe — Supporting Material

This repository contains the supporting material for the manuscript:

**LLM-Assisted Active Differential Diagnosis of Power-System Simulation Anomalies with Evidence-Grounded Verification**

The repository is intended to support scientific transparency and reviewer inspection of the **primary Qwen2.5-32B evaluation**. It is not presented as a complete software-reproduction package.

## What is included

| Location | Purpose |
|---|---|
| [`supplementary/CausalProbe_Supplementary_Material.md`](supplementary/CausalProbe_Supplementary_Material.md) | Integrated Supplementary Notes S1–S6 |
| [`data/candidate_mechanism_registry.csv`](data/candidate_mechanism_registry.csv) | Frozen symptom-conditioned candidate-mechanism registry |
| [`data/six_case_physical_details.csv`](data/six_case_physical_details.csv) | Frozen physical definitions and verification responses for the six diagnostic cases |
| [`results/main_results_summary.csv`](results/main_results_summary.csv) | Primary overall and case-level outcome summaries |
| [`results/statistical_results.md`](results/statistical_results.md) | Final case-stratified, non-paired statistical analysis |
| [`results/representative_traces_and_ablation.md`](results/representative_traces_and_ablation.md) | Representative trajectories for cases 01, 04, and 06, plus the auxiliary planning ablation |

## Scientific scope

The primary benchmark contains six frozen diagnostic cases spanning three reported symptom families:

- low voltage;
- branch thermal overload;
- power-flow nonconvergence.

For each symptom family, diagnosis is performed over a bounded, predefined mechanism registry. Each frozen case instantiates one primary mechanism. The benchmark therefore evaluates **mechanism discrimination under symptom ambiguity**, rather than exhaustive open-world root-cause discovery or arbitrary multi-fault diagnosis.

The trusted reference states used in verification are executable restoration targets. They are not root-cause labels or diagnostic oracles.

## Replay fairness

The formal stochastic evaluation used a replay environment frozen before evaluation. Every evidence-producing diagnostic action admissible for a frozen case and exposed to either method had a predefined replay response.

Across the six cases:

- 40/40 case × admissible evidence-action instances were covered;
- the same case + action returned the same underlying simulator evidence to Generic ReAct and CausalProbe;
- replay availability did not depend on method identity, current hypothesis state, previous stochastic trajectory, or eventual diagnosis;
- six covered responses contained predefined unavailable fields, so complete replay coverage does not imply universally complete or decisive evidence.

Replay responses were constructed from captured read-only PowerFactory state inspections, deterministic comparisons or calculations over frozen states, and previously executed and validated mechanism-specific restoration/cross-probe experiments. This statement should **not** be interpreted as claiming that every replay response was independently rerun in PowerFactory.

## Primary evaluation

The primary formal evaluation uses:

- Qwen2.5-32B-Instruct;
- temperature = 0.2;
- top-p = 0.8;
- 20 stochastic repetitions per case and method;
- 120 Generic ReAct episodes and 120 CausalProbe episodes;
- temporally interleaved execution;
- case-stratified, non-paired statistical inference.

The main manuscript distinguishes **mechanism correctness** from **grounded closure**. A correct mechanism label does not by itself satisfy the evidence requirements for diagnostic acceptance.

## What is intentionally not included

This repository does not attempt to publish every internal development or runtime artifact. In particular, this release does not include the complete PowerFactory project files, all runtime scripts, full raw LLM transcripts, complete internal replay payloads, or intermediate development/debug logs.

The purpose of this repository is to expose the manuscript-facing mechanism registry, frozen case definitions, evidence semantics, fairness audit, primary result summaries, statistical analysis, representative trajectories, and ablation evidence needed to inspect the scientific claims.

## Cross-LLM robustness

This release contains the primary Qwen2.5-32B evaluation only. Cross-LLM robustness results are outside the scope of the present release.

## Suggested reading order

1. Read the integrated Supplementary Material.
2. Inspect the candidate registry and six-case physical-detail CSV files for the frozen benchmark definitions.
3. Inspect the primary results and statistical analysis.
4. Use the representative trajectories to follow the evidence semantics in cases 01, 04, and 06.

## Citation

If you use these materials, please cite the associated manuscript. A repository-specific citation file can be added after the manuscript bibliographic information is finalized.
