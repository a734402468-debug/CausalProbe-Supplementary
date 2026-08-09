# Statistical Results

> The formal evaluation consists of case-stratified stochastic repetitions executed using a temporally interleaved schedule. Because method invocations did not share stochastic seeds or matched random perturbations, the formal outcomes are not treated as intrinsically paired observations.

The primary analysis uses the final conservative case-stratified, non-paired audit of the frozen 120 Generic ReAct and 120 CausalProbe episodes.

## Table A — Binary outcomes

Risk differences are CausalProbe minus Generic ReAct. Confidence intervals are the frozen stratified-bootstrap 95% intervals. The exact p-value is the primary case-stratified conditional permutation result; the CMH-type p-value is secondary.

| Outcome | Generic ReAct | CausalProbe | Risk difference (95% CI) | Exact stratified p | Secondary CMH-type p |
|---|---:|---:|---:|---:|---:|
| Mechanism correctness | 92.5% (111/120) | 100.0% (120/120) | +7.5 pp (+4.17, +10.83) | 0.0012285 | 0.0007657 |
| Grounded closure | 68.3% (82/120) | 100.0% (120/120) | +31.7 pp (+26.67, +36.67) | 5.4743×10^-18 | 1.7588×10^-14 |
| False/unsupported closure | 31.7% (38/120) | 0.0% (0/120) | -31.7 pp (-36.67, -26.67) | 5.4743×10^-18 | 1.7588×10^-14 |

## Table B — Path/count outcomes

Differences are CausalProbe minus Generic ReAct. Relative reduction is referenced to the Generic ReAct mean. Each p-value is from the frozen two-sided case-stratified permutation analysis.

| Outcome | Generic mean; median [IQR] | CausalProbe mean; median [IQR] | Mean difference (bootstrap 95% CI) | Relative reduction | Cliff's delta | Stratified p |
|---|---:|---:|---:|---:|---:|---:|
| Diagnostic path length | 6.508; 7 [1] | 2.567; 2 [1] | -3.942 (-4.025, -3.858) | 60.56% | -1.000 | 9.9999×10^-6 |
| Read-only inspections | 4.592; 5 [1] | 1.342; 1 [1] | -3.250 (-3.308, -3.192) | 70.78% | -1.000 | 9.9999×10^-6 |
| Mechanism verifications / active probes | 1.917; 2 [0] | 1.225; 1 [0] | -0.692 (-0.742, -0.642) | 36.09% | -0.692 | 9.9999×10^-6 |
| LLM/planner decisions | 7.875; 8 [1] | 2.567; 2 [1] | -5.308 (-5.583, -5.100) | 67.41% | -1.000 | 9.9999×10^-6 |

## Sensitivity note

The earlier episode-index McNemar/Wilcoxon analysis was retained only as sensitivity analysis. None of the audited substantive conclusions changed.
