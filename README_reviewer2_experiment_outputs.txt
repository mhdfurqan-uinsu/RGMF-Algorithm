HAM10000 RG-MF Reviewer-2 Rebuttal Experiment Outputs
======================================================================

Main files:
1. all_results_repeated_seed.csv
2. table_repeated_seed_mean_std_ci.csv
3. table_degradation_summary.csv
4. table_paired_bootstrap_significance.csv
5. table_reliability_verification_summary.csv
6. table_rg_mf_inference_cue_ablation_summary.csv
7. table_cost_indicator_summary.csv

Reviewer 2 mapping:
- Single split/single run: answered by repeated SEEDS and grouped splits.
- No CI/significance: answered by bootstrap CI and paired bootstrap test.
- Non-monotonic stress: answered by deterministic nested stress masks and mean±SD across seeds.
- Reliability head collapse: answered by r_target anchoring and reliability correlation analysis.
- No availability/plausibility ablation: answered by inference cue ablation.
- No cost indicator: answered by parameter and latency table.
- MAE/RMSE: removed from all main metrics.
