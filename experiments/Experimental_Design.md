# MOSAIC Experimental Design

## Common design

All experiments use the validated MOSAIC baseline unless a parameter is
explicitly varied.

- Random seeds: 1–30
- Repetitions: one run per seed-condition combination
- Run metrics every step: disabled
- Maximum duration: 500 ticks
- Experimental design: paired by random seed
- NetLogo version: 7.0.4

## Baseline configuration

- Robots: 15
- Initial energy: 150
- Energy heterogeneity: 0.30
- Capability heterogeneity: 0.50
- Tasks: 30
- Cooperative-task proportion: 0.40
- Communication radius: 8
- Communication failure rate: 0.05
- Reward regime: equal-share
- Reputation: enabled
- Adaptive strategies: enabled
- Initial strategy: mixed
- Mission incentive weight: 0.25 in the general interface baseline; BS07
  uses the mission-aligned regime and varies the incentive weight directly

## Experiment register

| ID | Experiment | Conditions | Runs | Research purpose |
|---|---|---|---:|---|
| BS01 | Baseline Replication | Baseline only | 30 | Estimate stochastic performance and establish the control condition. |
| BS02 | Reward Regime Comparison | leader-priority, equal-share, contribution-based, mission-aligned | 120 | Test effects of reward allocation on performance, coordination, and inequality. |
| BS03 | Communication Degradation | radius 4/8 × failure rate 0.05/0.35 | 120 | Separate structural reach from temporary link unreliability. |
| BS04 | Capability Heterogeneity | 0.0, 0.5, 1.0 | 90 | Test complementarity against capability-matching complexity. |
| BS05 | Cooperative Task Demand | 0.0, 0.4, 0.8 | 90 | Identify coalition-formation capacity limits. |
| BS06 | Reputation × Adaptation | Off/On × Off/On | 120 | Test the mission value of reliability learning and strategy adaptation. |
| BS07 | Mission Incentive Strength | 0.00, 0.10, 0.25, 0.50 | 120 | Test incentive saturation and cost-effectiveness. |

## Integrated results

The complete analysis supports the following conclusions:

1. Coordination capacity is the principal bottleneck.
2. Moderate capability heterogeneity is optimal in the tested environment.
3. Communication reach is more consequential than moderate temporary link failure.
4. High cooperative demand creates a nonlinear overload threshold.
5. Leader-priority rewards increase friction and inequality without improving completion.
6. Reputation and adaptation are context-dependent rather than universal baseline improvements.
7. Stronger mission incentives increase payouts without a reliable mission-performance gain.
