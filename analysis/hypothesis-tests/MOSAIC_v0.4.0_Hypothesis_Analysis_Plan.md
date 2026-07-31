# MOSAIC v0.4.0 — Hypothesis Analysis Plan

## Analytical principle

All experimental comparisons preserve the paired random-seed design. Each seed
acts as a matched block across conditions. Non-parametric repeated-measures
tests are used because several outcomes are discrete, bounded, skewed, or
contain ties.

## Hypotheses

### H1 — Baseline mission viability

Under the validated baseline, decentralized coordination will achieve task
completion above the 90% operational benchmark while all recorded run-level
invariants remain valid.

- Experiment: BS01
- Primary outcome: Completion Rate (%)
- Analysis: descriptive statistics, bootstrap confidence interval, one-sample
  Wilcoxon signed-rank test against 90%
- Validation: recorded task-state, reputation-bound, and strategy-validity
  checks

### H2 — Reward-regime trade-off

Leader-priority reward allocation will increase reward inequality and
coordination friction relative to equal-share without improving task
completion.

- Experiment: BS02
- Primary performance outcome: Completion Rate (%)
- Distributional outcome: Reward Gini
- Coordination-friction outcome: Unresolved Auction Rounds
- Analysis: Friedman tests and planned paired Wilcoxon comparisons with Holm
  correction

### H3 — Communication structure

Structural communication reach will have a larger beneficial effect on mission
outcomes than a moderate increase in temporary link failure.

- Experiment: BS03
- Factors: communication radius 4/8 and failure rate 0.05/0.35
- Primary outcome: Completion Rate (%)
- Secondary outcomes: Information Coverage (%), Mean Coalition Delay,
  Coordination Overhead, Mission Efficiency
- Analysis: within-seed 2×2 main-effect and interaction contrasts tested
  against zero

### H4 — Capability heterogeneity optimum

Moderate capability heterogeneity (0.5) will outperform both homogeneous (0.0)
and highly heterogeneous (1.0) teams.

- Experiment: BS04
- Primary outcome: Completion Rate (%)
- Secondary outcome: Mission Efficiency
- Analysis: Friedman test, planned moderate-versus-extremes contrast, and
  paired comparisons with Holm correction

### H5 — Cooperative-demand threshold

Cooperative-task demand will create a nonlinear coordination-capacity
threshold, with the 0.8 condition causing a disproportionately larger
completion decline than the 0.4 condition.

- Experiment: BS05
- Primary outcome: Completion Rate (%)
- Secondary outcomes: Failed — Auction/No Feasible, Coalition Formation
  Efficiency (%), Mean Coalition Delay
- Analysis: Friedman test and a planned second-difference threshold contrast

### H6 — Reputation and adaptation synergy

Reputation and adaptive strategies will improve mission outcomes most strongly
when jointly enabled.

- Experiment: BS06
- Factors: reputation Off/On and adaptation Off/On
- Primary outcome: Completion Rate (%)
- Secondary outcome: Mean Coalition Delay
- Analysis: within-seed 2×2 main-effect and interaction contrasts with Holm
  correction

### H7 — Mission-incentive saturation

Increasing mission-incentive strength will exhibit diminishing returns:
payments will rise monotonically while task completion will not reliably
improve.

- Experiment: BS07
- Primary performance outcome: Completion Rate (%)
- Economic outcome: Rewards Distributed
- Secondary outcome: Coalition Success (%)
- Analysis: Friedman tests, paired comparisons against zero incentive with
  Holm correction, and incremental cost-performance analysis

## Significance and reporting

- Alpha: 0.05
- All tests are two-sided unless a directional planned hypothesis explicitly
  requires a one-sided test.
- Report effect sizes, paired mean or median differences, adjusted p-values,
  and exact condition-level descriptive statistics.
- Statistical non-significance is not treated as evidence of equivalence.
- Findings apply only to the implemented model and tested parameter ranges.
