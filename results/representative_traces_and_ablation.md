# Representative Diagnostic Trajectories and Auxiliary Ablation

## Representative trajectory A — case_01

1. The monitored bus, **40 West End**, was at **0.9357532670435845 pu**, below the approved state.
2. Competing candidates included generator voltage-control configuration, excessive local reactive demand, generator reactive-limit saturation, shunt state/capability, and local topology.
3. Corrected observational evidence showed `G 40_down` in `constq`, rather than its trusted `constv` state. Its representative reactive output was -60.0 Mvar with `cQ_min=-200.0` and `cQ_max=200.0` Mvar, so it was within its corrected limits. `G 42_down` was at/flagged at its upper reactive boundary: Q=40.00000000029666 Mvar, `cQ_min=-30.0`, `cQ_max=40.0` Mvar.
4. `G 42_down` being at a reactive boundary did not establish mechanism-specific SUPPORT. **Reactive-power-boundary observation is observationally compatible with a Q-limit explanation but is not itself mechanism-specific SUPPORT.**
5. Active restoration changed `G 40_down` from `constq` to `constv`; voltage rose from 0.9357532670435845 to 0.9700000286119645 pu.
6. The predefined mapper returned deterministic `SUPPORT`; rollback and cleanup were verified; the controller made a grounded closure on `generator_voltage_control_configuration`.

This trajectory is not an exhaustive elimination of every alternative hypothesis.

## Representative trajectory B — case_04

1. Line `lne_49_69_1` was loaded at **113.15485801164138%**.
2. The line-rating candidate was checked against the trusted state. `TypLne.sline` was already **0.20000000298023224 kA**, exactly the reference value.
3. `thermal_line_rating_restoration` produced a verified numerical change of **0.0 kA**; loading remained **113.15485801164138%**. This is a **reference-state consistency refutation**, classified `VERIFIED_NO_OP_REFUTATION`, and not an active intervention.
4. The local physical load/injection mechanism was then considered. `thermal_physical_state_restoration` changed `lod_49_1.plini` from **140.0 to 80.0 MW**.
5. This genuine **active counterfactual verification** reduced loading from **113.15485801164138% to 97.86303838828825%** while the rating remained unchanged.
6. The mapper returned deterministic `SUPPORT`; rollback and cleanup were verified; grounded closure followed.

## Representative trajectory C — case_06

| Method | Correct mechanism label | Grounded closure | Defining evidence pattern |
|---|---:|---:|---|
| Generic ReAct | 20/20 | 0/20 | Correct labels without the required mechanism-specific supported closure |
| CausalProbe | 20/20 | 20/20 | Physical-state restoration, deterministic `SUPPORT`, verified rollback/cleanup |

The supported publication description is **physical-operating-stress-induced load-flow nonconvergence**. The physical-state restoration changed the frozen five-load operating state to its approved reference while keeping the approved configuration fingerprint invariant; the load flow changed from nonconverged (return code 1) to converged (return code 0).

**Correct mechanism label ≠ evidence-grounded diagnosis.**

## Auxiliary planning ablation

| Variant | n | Correctness | Grounded closure | Mean path |
|---|---:|---:|---:|---:|
| No-Discriminative-Planning | 30 | 30/30 (100%) | 30/30 (100%) | 2.33 |
| Full CausalProbe formal reference | 120 | 120/120 (100%) | 120/120 (100%) | 2.57 |

> The auxiliary ablation did not demonstrate a measurable efficiency contribution from the explicit coverage-based inspection-ranking heuristic in this benchmark.

The comparison does not establish that ranking is harmful or generally unnecessary.
