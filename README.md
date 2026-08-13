# Supporting Material

This repository contains the supporting material for the manuscript:

**LLM-Assisted Active Differential Diagnosis of Power-System Simulation Anomalies with Evidence-Grounded Verification**

The repository is intended to support scientific transparency and reviewer inspection of the manuscript's experimental design, evidence semantics, fairness protocol, primary results, representative diagnostic trajectories, auxiliary ablation, and cross-backbone robustness evaluation.

It is **not** presented as a complete software-reproduction package.

---

## 1. Study Scope

CausalProbe formulates power-system simulation-anomaly diagnosis as an **LLM-assisted active differential-diagnosis problem** over a bounded, symptom-conditioned set of competing mechanism hypotheses.

The benchmark contains six frozen diagnostic cases covering three reported symptom families:

- low voltage;
- branch thermal overload;
- power-flow nonconvergence.

For each symptom family, multiple candidate mechanisms are predefined before case-level diagnosis, while each frozen case instantiates one primary mechanism. The evaluation therefore focuses on **mechanism discrimination under symptom ambiguity**, rather than exhaustive open-world root-cause discovery or arbitrary multi-fault diagnosis.

A central distinction in the manuscript is:

> **A correct mechanism label does not by itself constitute an evidence-grounded diagnosis.**

Diagnostic acceptance requires mechanism-specific support under predefined evidence relations and verified simulator-state restoration.

---

## 2. Repository Contents

```text
CausalProbe-Supplementary/
├── README.md
├── supplementary/
│   └── CausalProbe_Supplementary_Material.md
├── data/
│   ├── candidate_mechanism_registry.csv
│   └── six_case_physical_details.csv
└── results/
    ├── main_results_summary.csv
    ├── statistical_results.md
    ├── representative_traces_and_ablation.md
    ├── cross_backbone_summary.csv
    ├── cross_backbone_per_case.csv
    ├── fig2_grounded_closure.csv
    └── fig2_safety_ceiling.csv
```

### Integrated Supplementary Material

[`supplementary/CausalProbe_Supplementary_Material.md`](supplementary/CausalProbe_Supplementary_Material.md)

This file contains Supplementary Notes **S1–S7**:

- **S1** — Candidate Mechanism Registry
- **S2** — Definitions of the Six Diagnostic Cases
- **S3** — Diagnostic Evidence Relations
- **S4** — Experimental Protocol and Fairness
- **S5** — Detailed Primary Results and Representative Trajectories
- **S6** — Auxiliary Planning Ablation
- **S7** — Cross-Backbone Robustness Evaluation

### Frozen Benchmark Definitions

- [`data/candidate_mechanism_registry.csv`](data/candidate_mechanism_registry.csv)  
  Frozen symptom-conditioned candidate-mechanism registry.

- [`data/six_case_physical_details.csv`](data/six_case_physical_details.csv)  
  Frozen physical definitions, reference values, and verification responses for the six diagnostic cases.

### Primary Evaluation Results

- [`results/main_results_summary.csv`](results/main_results_summary.csv)  
  Overall and case-level results for the primary Qwen2.5-32B-Instruct evaluation.

- [`results/statistical_results.md`](results/statistical_results.md)  
  Final case-stratified, non-paired statistical analysis.

- [`results/representative_traces_and_ablation.md`](results/representative_traces_and_ablation.md)  
  Representative diagnostic trajectories and the auxiliary planning ablation.

### Cross-Backbone Robustness Results

- [`results/cross_backbone_summary.csv`](results/cross_backbone_summary.csv)  
  Overall results across the evaluated LLM backbones.

- [`results/cross_backbone_per_case.csv`](results/cross_backbone_per_case.csv)  
  Per-case cross-backbone results.

- [`results/fig2_grounded_closure.csv`](results/fig2_grounded_closure.csv)  
  Data used for the grounded-closure panel of the cross-backbone figure.

- [`results/fig2_safety_ceiling.csv`](results/fig2_safety_ceiling.csv)  
  Data used for the safety-ceiling termination panel of the cross-backbone figure.

---

## 3. Primary Evaluation

The primary formal evaluation uses **Qwen2.5-32B-Instruct** with:

- temperature = 0.2;
- top-p = 0.8;
- 20 stochastic repetitions per case and method;
- 120 Generic ReAct episodes;
- 120 CausalProbe episodes;
- 240 total formal diagnostic episodes;
- temporally interleaved execution;
- case-stratified, non-paired statistical inference.

Across the primary evaluation:

- mechanism correctness increased from **92.5%** for Generic ReAct to **100%** for CausalProbe;
- grounded closure increased from **68.3%** to **100%**;
- unsupported or premature closure decreased from **31.7%** to **0%**;
- mean diagnostic path length decreased from **6.51** to **2.57** actions.

The primary evaluation is the main experimental basis of the manuscript.

---

## 4. Replay Fairness and Evidence Provenance

The formal stochastic evaluation used a replay environment frozen before evaluation.

Every evidence-producing diagnostic action that was admissible for a frozen case and exposed to either method had a predefined deterministic replay response before stochastic evaluation.

Across the six frozen cases:

- **40/40** case × admissible evidence-action instances were covered;
- the same case + diagnostic action returned the same underlying simulator evidence to Generic ReAct and CausalProbe;
- replay responses did not depend on method identity, current hypothesis state, preceding stochastic trajectory, or eventual diagnosis;
- six covered responses contained predefined unavailable fields, so complete replay coverage does not imply universally complete or decisive evidence.

Replay responses were constructed from:

- captured read-only PowerFactory state inspections;
- deterministic comparisons or calculations over frozen states; and
- previously executed and validated mechanism-specific restoration or cross-probe experiments.

This should **not** be interpreted as claiming that every replay response was independently rerun in PowerFactory.

CausalProbe's hypothesis-management, candidate-action mapping, and evidence-processing logic constrain how common simulator evidence is organized and interpreted; they do not introduce additional simulator information.

---

## 5. Cross-Backbone Robustness Evaluation

A secondary robustness evaluation examines whether the diagnostic-method comparison persists across different LLM backbones and serving conditions.

The evaluated backbones are:

- **Qwen2.5-7B-Instruct**
- **Meta-Llama-3.1-8B-Instruct**
- **Qwen2.5-32B-Instruct**
- **DeepSeek-V4-Pro served through SiliconFlow**

Qwen2.5-7B-Instruct, Meta-Llama-3.1-8B-Instruct, and DeepSeek-V4-Pro were evaluated with 10 stochastic repetitions per case and method. The previously frozen 20-repetition Qwen2.5-32B-Instruct primary results were retained unchanged.

The cross-backbone evaluation is treated as **secondary robustness evidence**, not as a general-purpose model ranking or a scaling study.

One particularly informative result is obtained with DeepSeek-V4-Pro:

- Generic ReAct: **60/60 mechanism correct**, **21/60 grounded closure (35.0%)**;
- CausalProbe: **60/60 mechanism correct**, **60/60 grounded closure (100%)**;
- grounded-closure difference: **+65.0 percentage points**;
- 95% CI: **+56.7 to +73.3 percentage points**;
- exact case-stratified \(p = 8.85\times10^{-19}\).

All 39 non-grounded Generic ReAct episodes under DeepSeek-V4-Pro reported the correct mechanism but terminated before executing an available mechanism-specific verification.

This result reinforces the manuscript's central distinction between **mechanism correctness** and **evidence-grounded diagnostic closure**.

The compact-backbone conditions additionally expose failures to complete the diagnostic procedure within the prescribed action budget. These outcomes are reported through the **safety-ceiling termination rate**. Safety-ceiling termination is a diagnostic-process metric and should not be interpreted as a power-system security limit or as the complement of grounded closure.

---

## 6. Interpretation Boundaries

The materials in this repository support claims about the evaluated diagnostic workflow under the frozen benchmark.

They should **not** be used to claim:

- a monotonic LLM scaling law;
- a general ranking of the evaluated models;
- universal frontier-model behavior;
- identical hidden reasoning compute across providers;
- identical effective sampling mechanics across serving environments;
- statistical pairing of repetitions that did not share matched stochastic realizations;
- exhaustive coverage of all possible power-system simulation anomalies.

DeepSeek-V4-Pro was **served through SiliconFlow**; the experiment should not be described as using direct official DeepSeek API access.

---

## 7. What Is Intentionally Not Included

This repository does not attempt to publish every internal development or runtime artifact.

The current release does not include:

- complete PowerFactory project files;
- all runtime and orchestration scripts;
- complete raw LLM transcripts;
- full internal replay payloads;
- development/debug logs;
- internal writing-agent handoff materials.

The purpose of this repository is to expose the manuscript-facing materials needed to inspect the scientific claims: the frozen hypothesis registry, case definitions, evidence relations, replay-fairness audit, primary results, statistical analysis, representative trajectories, ablation evidence, and cross-backbone robustness results.

---

## 8. Suggested Reading Order

For reviewers or readers inspecting the study:

1. Read [`supplementary/CausalProbe_Supplementary_Material.md`](supplementary/CausalProbe_Supplementary_Material.md).
2. Inspect the frozen benchmark definitions in [`data/`](data/).
3. Inspect the primary results and statistical analysis in [`results/`](results/).
4. Use the representative trajectories to follow the evidence semantics in selected cases.
5. Inspect the cross-backbone summary and per-case files for the secondary robustness evaluation.

---

## 9. Citation

If you use these materials, please cite the associated manuscript:

**LLM-Assisted Active Differential Diagnosis of Power-System Simulation Anomalies with Evidence-Grounded Verification**

Formal bibliographic information will be added after publication.
