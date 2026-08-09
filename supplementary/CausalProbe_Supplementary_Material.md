# Supplementary Material

## LLM-Assisted Active Differential Diagnosis of Power-System Simulation Anomalies with Evidence-Grounded Verification

This document provides the manuscript-facing supplementary evidence for the primary evaluation of CausalProbe. It is organized to make the frozen mechanism registry, six-case construction, evidence semantics, replay fairness, primary results, representative diagnostic trajectories, and auxiliary planning ablation directly inspectable.

The supplementary material supports the claims in the manuscript but is not intended to constitute a complete software-reproduction package. Machine-readable supporting files are linked below where applicable.


## Scope and interpretation

The benchmark uses a bounded, symptom-conditioned hypothesis space and one instantiated primary mechanism per frozen case. The mechanism registry is not an exhaustive taxonomy of all possible power-system simulation failures. Observational compatibility is not treated as affirmative support, refutation of one candidate does not automatically support another, and insufficient evidence is represented as diagnostic abstention rather than as a causal mechanism.

## Supplementary Note S1 — Candidate Mechanism Registry

Machine-readable registry: [`../data/candidate_mechanism_registry.csv`](../data/candidate_mechanism_registry.csv)

The benchmark used a frozen, symptom-family-specific registry containing 17 causal mechanisms plus one family-specific unresolved diagnostic state per family.

| Family | Causal mechanisms (frozen internal ID) |
|---|---|
| Low voltage | Excessive local reactive demand (`excessive_local_reactive_demand`); generator reactive-power limit saturation (`generator_reactive_limit_saturation`); generator voltage-control configuration mismatch (`generator_voltage_control_configuration`); local topology weakening (`local_topology_weakening`); shunt compensation state/capability difference (`shunt_compensation_state_or_capability`) |
| Thermal overload | Explicit non-slack generator redispatch (`explicit_non_slack_generator_redispatch`); line thermal-rating parameter error (`line_thermal_rating_parameter`); local load/injection stress (`local_load_or_injection_stress`); neighboring branch/transformer outage or path change (`neighboring_branch_or_transformer_outage_or_path_change`); reactive-demand or power-factor deterioration (`reactive_demand_or_power_factor_deterioration`); transformer tap or phase-shift flow-control difference (`transformer_tap_or_phase_shift_flow_control`) |
| Power-flow nonconvergence | Discrete-control or limit conflict (`discrete_control_or_limit_conflict`); load-flow execution-configuration mismatch (`execution_load_flow_configuration`); supported basic model-data inconsistency (`obvious_model_data_inconsistency`); physical operating-point stress (`physical_operating_point_stress`); topology islanding or disconnection (`topology_islanding_or_disconnection`); unavailable reference or slack source (`unavailable_reference_or_slack_source`) |

`OTHER_INSUFFICIENT_EVIDENCE` is an abstention/unresolved diagnostic state and is excluded from the causal-mechanism count.

## Supplementary Note S2 — Definitions of the Six Diagnostic Cases

Frozen case details: [`../data/six_case_physical_details.csv`](../data/six_case_physical_details.csv)

| Case | System | Instantiated mechanism | Abnormal and reference states | Verification response |
|---|---|---|---|---|
| case_01 | IEEE 118-bus system | Generator voltage-control configuration mismatch | `G 40_down`: `constq→constv`; Bus 40 West End initially 0.9357532670435845 pu | Voltage increased to 0.9700000286119645 pu; `SUPPORT`. `G 40_down` was within Q bounds; `G 42_down` was at/flagged at its upper Q boundary. |
| case_02 | IEEE 118-bus system | Excessive local reactive demand | `lod_41_1.qlini`: 120.0→20.0 Mvar; initial voltage 0.9384765334115057 pu | Voltage increased to 0.9721609520739422 pu; `SUPPORT`. |
| case_03 | IEEE 118-bus system | Line thermal-rating parameter error | `lne_49_69_1` rating: 0.17297187447547913→0.20000000298023224 kA; load 80.0 MW | Loading decreased 113.15485843385993→97.86303838828825% with physical flow invariant; `SUPPORT`. |
| case_04 | IEEE 118-bus system | Local load or injection stress | Rating already 0.20000000298023224 kA; `lod_49_1.plini`: 140.0→80.0 MW | Zero-change rating check left loading at 113.15485801164138% (`REFUTE`); non-zero physical restoration reduced it to 97.86303838828825% (`SUPPORT`). |
| case_05 | South Xinjiang regional system | Load-flow execution-configuration mismatch | Approved configuration fingerprint changed from `f01b...8e43` to trusted `6aa6...5c21`; `iStepAdapt`: 1→0 | Return code 1→0 and convergence restored; `SUPPORT`. The configuration was treated as one mechanism. |
| case_06 | South Xinjiang regional system | physical-operating-stress-induced load-flow nonconvergence | Five-load physical state restored to approved values while configuration fingerprint `6aa6...5c21` remained invariant | Return code 1→0 and convergence restored; `SUPPORT`. This does not establish mathematical infeasibility, voltage collapse, or instability. |

## Supplementary Note S3 — Diagnostic Evidence and Verification Relations

Three frozen evidence classes were used.

| Evidence class | Role | Permitted causal interpretation |
|---|---|---|
| `OBSERVATIONAL_EVIDENCE` | Read factual state and plausibility context | May adjust plausibility; cannot produce affirmative mechanism-specific `SUPPORT` |
| `REFERENCE_STATE_CONSISTENCY_OR_REFUTATION` | Compare an observable field or fingerprint with its trusted reference | May deterministically `REFUTE` a candidate under its predefined matching rule; cannot by itself produce active mechanism-specific `SUPPORT` |
| `ACTIVE_COUNTERFACTUAL_VERIFICATION` | Temporarily restore a selected abnormal field/group, evaluate the predefined response/invariants, then roll back | May produce deterministic `SUPPORT` or `REFUTE`; non-zero state change is required for affirmative `SUPPORT` |

The main mechanism-specific active relations were:

| Family | Mechanism | Reference-state relation | Active verification and `SUPPORT` rule |
|---|---|---|---|
| Low voltage | Excessive local reactive demand | Matching local P/Q pattern refutes changed demand | Restore abnormal reactive demand; require voltage recovery with competing control state/topology invariant |
| Low voltage | Generator voltage-control configuration mismatch | Matching control mode/setpoint refutes mismatch | Restore abnormal control field; require voltage recovery with competing demand/topology invariant |
| Thermal overload | Line thermal-rating parameter error | Trusted rating already present refutes rating difference | Restore a non-trusted rating; require loading recovery while physical flow/injections remain invariant |
| Thermal overload | Local load or injection stress | Matching local setpoint pattern refutes stress | Restore physical state; require material flow/loading reduction with rating/topology invariant |
| Nonconvergence | Load-flow execution-configuration mismatch | Matching approved fingerprint refutes mismatch | Restore abnormal configuration field group; require convergence with physical state/topology invariant |
| Nonconvergence | Physical operating-point stress | Matching stress-group setpoints refute change | Restore abnormal physical field group; require convergence with configuration/topology invariant |

For mechanisms without a frozen active rule, the benchmark provided observational and/or reference-state refutation evidence but no affirmative active `SUPPORT` route. This prevents a read-only observation from being reclassified post hoc as causal proof.

Case_04 is the canonical distinction. The rating was already trusted; reapplying it changed `sline` by 0.0 kA and left loading unchanged, so the event was a **reference-state consistency refutation** (`VERIFIED_NO_OP_REFUTATION`). Restoring `lod_49_1.plini` from 140.0 to 80.0 MW was a genuine **active counterfactual verification**; loading fell from 113.15485801164138% to 97.86303838828825%, producing `SUPPORT`.

Grounded closure required exactly one candidate backed by deterministic active `SUPPORT`, a `SUPPORTED` ledger state, verified rollback and cleanup, and no active `REFUTE` for that candidate. It did not require exhaustive refutation of every other plausible candidate.

The predefined replay responses were constructed from captured read-only PowerFactory state inspections, deterministic comparisons or calculations over frozen simulator states, and previously executed and validated mechanism-specific restoration/cross-probe experiments. This provenance does not imply that every possible replay response was independently rerun in PowerFactory.

## Supplementary Note S4 — Experimental Protocol and Fairness

| Item | Frozen setting |
|---|---|
| Systems | IEEE 118-bus system; South Xinjiang regional system |
| Model and sampling | Qwen2.5-32B-Instruct; temperature 0.2; top-p 0.8; no request-level seed |
| Request timeout | 120 s |
| Formal evaluation | 6 cases × 20 repetitions × 2 methods = 240 replay-only episodes |
| Scheduling | Temporally interleaved; 60 Generic-first and 60 CausalProbe-first case/repetition blocks |
| Shared inputs | Same model, initial observations, candidate descriptions, methodologically applicable evidence/actions, and safety ceiling |
| Primary inference | Case-stratified, non-paired stochastic analysis |

The method invocations did not share seeds, RNG states, or matched random perturbations. Episode-index paired tests were therefore retained only as sensitivity analyses.

### Replay Coverage and Trajectory Independence

Every evidence-producing diagnostic action admissible for a frozen case and exposed to either method had a predefined replay response before stochastic evaluation. Across the six cases, 40/40 case × admissible action instances were covered (100%). For the same case and action, Generic ReAct and CausalProbe received the same underlying simulator evidence; availability did not depend on method identity, the current hypothesis, prior stochastic outcomes, or the eventual trajectory. Six covered responses contained predefined unavailable fields, so coverage did not imply universally conclusive evidence. CausalProbe's hypothesis-ledger and evidence-processing logic did not introduce additional simulator information.

| Symptom family | Evidence/action class | Covered / admissible | Same underlying response for both methods? |
|---|---|---:|---|
| Low voltage | Read-only inspections; mechanism-specific restoration | 12 / 12 | Yes |
| Thermal overload | Read-only inspections; mechanism-specific restoration | 14 / 14 | Yes |
| Nonconvergence | Read-only/reference checks; mechanism-specific restoration | 14 / 14 | Yes |
| **Total** | **All admissible evidence-producing actions** | **40 / 40 (100%)** | **Yes** |

## Supplementary Note S5 — Detailed Results and Representative Diagnostic Trajectories

Supporting result files: [`../results/main_results_summary.csv`](../results/main_results_summary.csv), [`../results/statistical_results.md`](../results/statistical_results.md), and [`../results/representative_traces_and_ablation.md`](../results/representative_traces_and_ablation.md).

| Method | Correct | Grounded | Unsupported/premature closure | Mean path | Mean inspections | Mean active probes | Mean planner decisions |
|---|---:|---:|---:|---:|---:|---:|---:|
| Generic ReAct | 111/120 (92.5%) | 82/120 (68.3%) | 38/120 (31.7%) | 6.51 | 4.59 | 1.92 | 7.88 |
| CausalProbe | 120/120 (100.0%) | 120/120 (100.0%) | 0/120 (0.0%) | 2.57 | 1.34 | 1.23 | 2.57 |

| Case | Generic correct / grounded / mean path | CausalProbe correct / grounded / mean path |
|---|---:|---:|
| case_01 | 11/20 / 11/20 / 6.00 | 20/20 / 20/20 / 2.00 |
| case_02 | 20/20 / 11/20 / 5.50 | 20/20 / 20/20 / 2.00 |
| case_03 | 20/20 / 20/20 / 7.00 | 20/20 / 20/20 / 2.00 |
| case_04 | 20/20 / 20/20 / 7.00 | 20/20 / 20/20 / 3.40 |
| case_05 | 20/20 / 20/20 / 6.55 | 20/20 / 20/20 / 2.65 |
| case_06 | 20/20 / 0/20 / 7.00 | 20/20 / 20/20 / 3.35 |

Grounded Success@k denotes the number of episodes that achieved grounded closure within at most $k$ diagnostic actions. Grounded Success@k for Generic ReAct versus CausalProbe was 0/0 at k=1, 0/67 at k=2, 0/105 at k=3, 0/120 at k=4, 1/120 at k=5, 31/120 at k=6, and 82/120 at k=7.

**case_01 trajectory.** The corrected reactive evidence distinguished `G 40_down` (within corrected limits) from `G 42_down` (at/flagged at its upper boundary). The boundary observation was compatible with a Q-limit explanation but was not mechanism-specific `SUPPORT`. Restoring `G 40_down` voltage control increased voltage from 0.9357532670435845 to 0.9700000286119645 pu and produced deterministic `SUPPORT` and grounded closure.

**case_04 trajectory.** The already-trusted rating and persistent overload produced a zero-change reference-state `REFUTE`. The subsequent non-zero load restoration reduced loading from 113.15485801164138% to 97.86303838828825% and produced active `SUPPORT` and grounded closure.

**case_06 trajectory.** Generic ReAct produced 20/20 correct labels but 0/20 grounded closures. CausalProbe produced 20/20 correct labels and 20/20 grounded closures after mechanism-specific physical verification, deterministic `SUPPORT`, and verified cleanup. Thus, **Correct mechanism label $\neq$ evidence-grounded diagnosis.**.

## Supplementary Note S6 — Auxiliary Inspection-Ranking Ablation

Representative traces and ablation record: [`../results/representative_traces_and_ablation.md`](../results/representative_traces_and_ablation.md)

The No-Discriminative-Planning auxiliary variant removed the explicit coverage-based inspection-ranking heuristic while retaining the remainder of the integrated workflow. The frozen experiment contained 30 episodes, five per case.

| Variant | n | Correctness | Grounded closure | Mean path |
|---|---:|---:|---:|---:|
| Full CausalProbe formal reference | 120 | 120/120 | 120/120 | 2.57 |
| No-Discriminative-Planning | 30 | 30/30 | 30/30 | 2.33 |

Predefined classification: `PLANNING_CONTRIBUTION_NOT_SUPPORTED`.

> The auxiliary experiment did not demonstrate that the explicit coverage-based inspection-ranking heuristic independently reduced diagnostic path length in this benchmark. Accordingly, the primary claims are made for the integrated active differential-diagnosis workflow rather than for the ranking heuristic in isolation.
