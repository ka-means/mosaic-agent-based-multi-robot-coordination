# MOSAIC Experimental Data Validation Report

## Validation scope

This report checks official raw BehaviorSpace run counts against the clean
processed datasets and the harmonized master dataset.

| Dataset | Embedded experiment | Source model | Export time | Expected runs | Raw runs | Processed rows | Result |
|---|---|---|---|---:|---:|---:|---|
| BS01_Baseline_Replication | BS01_Baseline_Replication_30_Seeds | MOSAIC_v0_3_integrated_final_Subm_ready.nlogo.nlogox | 07/20/2026 19:48:48:647 +1000 | 30 | 30 | 30 | PASS |
| BS02_Reward_Regime_Comparison | BS02_Reward_Regime_Comparison_30_Seeds | MOSAIC_v0_3_integrated_final_Subm_ready.nlogo.nlogox | 07/20/2026 20:32:45:127 +1000 | 120 | 120 | 120 | PASS |
| BS03_Communication_Degradation | BS03_Communication_Degradation_2x2_30_Seeds | MOSAIC_v0_3_integrated_final_Subm_ready.nlogo.nlogox | 07/20/2026 21:00:31:609 +1000 | 120 | 120 | 120 | PASS |
| BS04_Capability_Heterogeneity | BS04_Capability_Heterogeneity_30_Seeds | 1MOSAIC_v0_3_integrated_final_Subm_ready.nlogo.nlogox | 07/29/2026 20:25:03:454 +1000 | 90 | 90 | 90 | PASS |
| BS05_Cooperative_Task_Demand | BS05_Cooperative_Task_Demand_30_Seeds | 1MOSAIC_v0_3_integrated_final_Subm_ready.nlogo.nlogox | 07/29/2026 21:35:04:212 +1000 | 90 | 90 | 90 | PASS |
| BS06_Reputation_Adaptation | BS06_Reputation_Adaptation_30_Seeds | 1MOSAIC_v0_4_integrated_final_Subm_ready.nlogo.nlogox | 07/30/2026 18:26:23:807 +1000 | 120 | 120 | 120 | PASS |
| BS07_Mission_Incentive_Strength | BS07_Mission_Incentive_Strength_30_Seeds | 1MOSAIC_v0_4_integrated_final_Subm_ready.nlogo.nlogox | 07/30/2026 19:36:22:081 +1000 | 120 | 120 | 120 | PASS |
| MASTER | Harmonized BS01–BS07 dataset | Derived dataset |  | 690 |  | 690 | PASS |

## Conclusion

The seven official raw exports contain the expected 690 runs, and every
experiment has the same number of processed rows as raw runs. The master
dataset contains exactly 690 harmonized records.