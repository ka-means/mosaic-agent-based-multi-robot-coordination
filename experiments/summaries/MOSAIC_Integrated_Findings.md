# MOSAIC Integrated Experimental Findings

## Scope

The integrated experimental program contains seven BehaviorSpace experiments, 690 total runs, and 30 paired random seeds per condition.

## Baseline

- Mean mission completion: 92.22%
- Mean completed tasks: 27.67 of 30
- Mean information coverage: 95.16%
- Mean coalition success: 78.24%
- Mean coalition formation efficiency: 4.79%
- Mean coordination overhead: 75.40
- Mean mission efficiency: 1.482
- Mean Reward Gini: 0.296

## Integrated conclusions

1. **Coordination capacity, not task execution, is the dominant system constraint.** Formed coalitions frequently succeeded, but partner matching, feasible allocation, and coalition formation produced delays, unresolved auctions, and task failures.

2. **Moderate capability heterogeneity is optimal.** The 0.5 condition significantly outperformed both homogeneous and highly heterogeneous teams, indicating an intermediate balance between complementarity and matching complexity.

3. **Communication reach matters more than moderate temporary link unreliability.** Increasing the radius from 4 to 8 improved completion, coverage, network connectivity, coalition delay, and coordination overhead. Raising the failure rate from 0.05 to 0.35 did not significantly reduce completion.

4. **High cooperative demand creates a nonlinear overload threshold.** Completion fell from 98.89% with no cooperative tasks to 92.22% at the baseline 0.4 level and 75.67% at 0.8. The second decline was significantly larger.

5. **Reward allocation changes process and fairness more than mission completion.** The four reward regimes did not differ significantly in completion. Leader-priority produced more unresolved auction rounds and higher inequality.

6. **Reputation and adaptation are not universal baseline improvements.** Neither mechanism had a significant main completion effect. Their combination produced a targeted reduction in coalition formation delay, suggesting context-dependent process value.

7. **Mission incentives saturate.** A low incentive produced the best descriptive completion, but no incentive level delivered a statistically reliable mission benefit. Moderate and high incentives increased payments substantially while completing fewer tasks on average than zero bonus.

## Recommended baseline configuration

- capability-heterogeneity: 0.5
- communication-radius: 8
- communication-failure-rate: 0.05
- coalition-task-proportion: approximately 0.4 or lower unless formation mechanisms are improved
- reward-regime: equal-share
- reputation-enabled?: true, retained as an experimental mechanism rather than a proven completion enhancer
- adaptive-strategies?: true, retained for process experimentation
- mission-incentive-weight: 0.0; 0.10 only as a low-cost experimental alternative

## Main model-development priority

Improve decentralized partner discovery and coalition formation before adding stronger economic incentives. Candidate mechanisms include capability indexing, coalition pre-commitment, targeted information routing, relay agents, and more selective coalition-search procedures.

## Experimental status

- Experiments complete: 7
- Total runs: 690
- Verification status: all experiments passed task-state, energy, reward, reputation-bound, and strategy-validity checks.
