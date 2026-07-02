# RGMF-Algorithm

**Reliability-Gated Multimodal Fusion Algorithm for Teledermatology Under Missing and Corrupted Metadata**

This repository provides the source code, experimental notebook, metadata files, result tables, training histories, and generated figures associated with the manuscript:

**Reliability-Gated Multimodal Fusion Algorithm for Teledermatology Under Missing and Corrupted Metadata**

The repository is prepared to support reproducibility, reviewer verification, and transparent reporting of the experiments conducted on the HAM10000 skin lesion dataset.

---

## 1. Overview

Teledermatology systems often rely on dermoscopic images together with structured clinical metadata such as age, sex, and anatomical site. However, in practical remote-care settings, metadata may be missing, inconsistently encoded, or clinically implausible. This repository implements and evaluates a reliability-gated multimodal fusion framework, referred to as **RG-MF**, which estimates sample-wise metadata reliability from availability and plausibility cues and attenuates metadata influence before multimodal fusion.

The repository includes experiments for:

* Vision Transformer image-only baseline.
* Prior multimodal fusion baseline.
* Proposed learned Reliability-Gated Multimodal Fusion model.
* Direct rule-based (r^*) metadata gate.
* Metadata-contribution diagnostic analysis.
* Reliability-gate verification.
* Deterministic missingness and corruption stress tests.
* Gate-mode ablation.
* Repeated-seed evaluation.
* Parameter-count and inference-latency analysis.

---

## 2. Repository Contents

The main files in this repository include:

| File                                                                   | Description                                                                                                                                      |
| ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `ham10000_rg_mf_reviewer3_4_additional_experiments_GOOGLE_COLAB.ipynb` | Main Google Colab notebook for additional reviewer experiments, including rule-based (r^*) gate, metadata diagnostics, and reliability analysis. |
| `HAM10000_metadata.csv`                                                | HAM10000 metadata file used for age, sex, anatomical-site, lesion label, and lesion-level grouping.                                              |
| `HAM10000_metadata.xls`                                                | Excel version of the HAM10000 metadata file.                                                                                                     |
| `all_results_repeated_seed.csv`                                        | Full repeated-seed experiment results.                                                                                                           |
| `all_results_partial.csv`                                              | Partial/backup experiment result file.                                                                                                           |
| `table_repeated_seed_mean_std_ci.csv`                                  | Summary table containing mean, standard deviation, and confidence interval values across seeds.                                                  |
| `table_degradation_summary.csv`                                        | Clean-to-severe metadata degradation summary.                                                                                                    |
| `table_paired_bootstrap_significance.csv`                              | Paired bootstrap comparison results.                                                                                                             |
| `table_reliability_verification_summary.csv`                           | Reliability verification summary under metadata degradation.                                                                                     |
| `table_rg_mf_inference_cue_ablation_summary.csv`                       | Gate-mode ablation summary.                                                                                                                      |
| `table_rg_mf_inference_cue_ablation_by_seed.csv`                       | Seed-wise gate-mode ablation results.                                                                                                            |
| `table_cost_indicator_summary.csv`                                     | Parameter-count and latency summary table.                                                                                                       |
| `cost_latency_params.csv`                                              | Raw or intermediate cost and latency indicators.                                                                                                 |
| `cost_partial.csv`                                                     | Partial/backup cost file.                                                                                                                        |
| `degradation_by_seed.csv`                                              | Seed-wise degradation results.                                                                                                                   |
| `training_histories.csv`                                               | Combined training-history log.                                                                                                                   |
| `history_seed42_*.csv`                                                 | Training histories for seed 42.                                                                                                                  |
| `history_seed123_*.csv`                                                | Training histories for seed 123.                                                                                                                 |
| `history_seed2025_*.csv`                                               | Training histories for seed 2025.                                                                                                                |
| `reliability_verification_by_seed.csv`                                 | Seed-wise reliability verification output.                                                                                                       |
| `fig_clean_macro_f1_mean_sd.png`                                       | Clean-setting macro-F1 figure.                                                                                                                   |
| `fig_macro_f1_missing_mean_sd.png`                                     | Metadata-missingness robustness figure.                                                                                                          |
| `fig_macro_f1_corruption_mean_sd.png`                                  | Metadata-corruption robustness figure.                                                                                                           |
| `fig_reliability_missing.png`                                          | Reliability response under missingness.                                                                                                          |
| `fig_reliability_corruption.png`                                       | Reliability response under corruption.                                                                                                           |
| `README_reviewer2_experiment_outputs.txt`                              | Reviewer-oriented output mapping for earlier revision experiments.                                                                               |

---

## 3. Dataset Requirement

This repository does **not** redistribute the HAM10000 dermoscopic image files. The user must obtain the HAM10000 image dataset from its original public source and place the image files in the expected image directory before running the notebook.

The metadata file is included in this repository as:

```text
HAM10000_metadata.csv
```

A typical local or Google Colab setup should contain:

```text
HAM10000_images/
    ISIC_*.jpg

HAM10000_metadata.csv
```

For Google Colab, users may upload or mount the dataset through Google Drive and update the dataset path inside the notebook.

Example path configuration:

```python
IMAGE_DIR = "/content/drive/MyDrive/ham10000/HAM10000_images"
METADATA_PATH = "/content/drive/MyDrive/ham10000/HAM10000_metadata.csv"
```

For local Windows use, update the paths according to the local directory:

```python
IMAGE_DIR = r"D:\RISETKOLABORASI\ham10000\HAM10000_images"
METADATA_PATH = r"D:\RISETKOLABORASI\ham10000\HAM10000_metadata.csv"
```

---

## 4. Models Evaluated

The repository supports the following model settings:

### 4.1 ViT Image-Only

A Vision Transformer baseline that uses only dermoscopic images and does not consume clinical metadata. This model serves as a strong unimodal reference.

### 4.2 Prior Fusion

The prior multimodal fusion pipeline that combines image features and metadata without reliability-gated metadata attenuation.

### 4.3 RG-MF

The proposed Reliability-Gated Multimodal Fusion framework. RG-MF estimates sample-wise metadata reliability from availability and plausibility cues and uses the reliability score to gate the metadata representation before multimodal fusion.

### 4.4 Rule-Based (r*) Gate

A direct diagnostic baseline where the metadata-quality proxy (r^*) itself is used as the sample-wise metadata gate. This baseline is included to test whether the learned reliability module provides value beyond a predefined quality rule.

### 4.5 Metadata-Contribution Diagnostics

Additional diagnostic settings are included to examine whether the classifier is image-dominated, including zero-metadata gating and metadata representation norm analysis.

---

## 5. Main Experimental Design

The experiments are designed to evaluate both predictive performance and robustness under imperfect metadata conditions.

Main settings:

* Dataset: HAM10000.
* Modalities: dermoscopic image and structured metadata.
* Metadata fields: age, sex, anatomical site.
* Seeds: 42, 123, and 2025.
* Split strategy: lesion-level grouped split using `lesion_id`.
* Stress types: missingness and corruption.
* Stress rates: 0.25, 0.50, and 0.75.
* Stress protocol: deterministic nested metadata degradation.
* Main metric: macro-F1.
* Additional metrics: accuracy, reliability score, metadata-quality proxy correlation, parameter count, and latency.

---

## 6. Metadata Reliability Design

RG-MF estimates metadata reliability using two cue families:

### 6.1 Availability Cues

Availability cues indicate whether each metadata field is observed.

```text
a = [a_age, a_sex, a_site]
```

### 6.2 Plausibility Cues

Plausibility cues indicate whether the observed value satisfies predefined validity checks.

```text
p = [p_age, p_sex, p_site]
```

### 6.3 Metadata-Quality Proxy

The metadata-quality proxy is computed as:

```text
r* = mean(a ⊙ p)
```

where `⊙` denotes element-wise multiplication.

### 6.4 Learned Reliability Gate

The learned reliability head maps availability and plausibility cues into a sample-wise reliability score:

```text
r = sigmoid(MLP(z_r))
```

The metadata representation is then gated as:

```text
M_g = r · M
```

---

## 7. Metadata Stress Testing

Metadata degradation is applied only to the structured metadata stream. Image inputs remain unchanged.

### 7.1 Missingness

Selected metadata fields are replaced with missing values or unknown tokens.

### 7.2 Corruption

Selected metadata fields are replaced with invalid, mismatched, or implausible values.

### 7.3 Plausibility Checking

Plausibility rules are applied to age, sex, and anatomical site fields to determine whether each field remains valid after degradation.

---

## 8. How to Run

### 8.1 Google Colab

1. Open the notebook:

```text
ham10000_rg_mf_reviewer3_4_additional_experiments_GOOGLE_COLAB.ipynb
```

2. Select GPU runtime:

```text
Runtime → Change runtime type → GPU
```

3. Mount Google Drive if the HAM10000 images are stored there.

4. Update the dataset paths:

```python
IMAGE_DIR = "/content/drive/MyDrive/ham10000/HAM10000_images"
METADATA_PATH = "/content/drive/MyDrive/ham10000/HAM10000_metadata.csv"
```

5. Run all cells.

### 8.2 Local Execution

Install the required Python packages, then run the notebook using Jupyter Notebook or JupyterLab.

Recommended environment:

```bash
python -m venv rgmf_env
source rgmf_env/bin/activate
pip install --upgrade pip
pip install torch torchvision torchaudio
pip install timm scikit-learn pandas numpy matplotlib pillow tqdm openpyxl
pip install jupyter
```

For Windows PowerShell:

```powershell
python -m venv rgmf_env
.\rgmf_env\Scripts\activate
python -m pip install --upgrade pip
pip install torch torchvision torchaudio
pip install timm scikit-learn pandas numpy matplotlib pillow tqdm openpyxl
pip install jupyter
```

Then launch:

```bash
jupyter notebook
```

Open the notebook and update the dataset paths.

---

## 9. Expected Outputs

After running the notebook, the following outputs can be generated or reproduced:

### 9.1 Repeated-Seed Performance

```text
table_repeated_seed_mean_std_ci.csv
all_results_repeated_seed.csv
```

These files summarize clean, missingness, and corruption performance across seeds.

### 9.2 Degradation Summary

```text
table_degradation_summary.csv
degradation_by_seed.csv
```

These files summarize the clean-to-severe metadata degradation behavior.

### 9.3 Reliability Verification

```text
table_reliability_verification_summary.csv
reliability_verification_by_seed.csv
fig_reliability_missing.png
fig_reliability_corruption.png
```

These files evaluate whether the learned reliability score follows metadata-quality degradation.

### 9.4 Gate-Mode Ablation

```text
table_rg_mf_inference_cue_ablation_summary.csv
table_rg_mf_inference_cue_ablation_by_seed.csv
```

These files compare full gate, availability-only gate, plausibility-only gate, no-gate, static-mean gate, and rule-based gate behavior.

### 9.5 Cost and Latency Indicators

```text
table_cost_indicator_summary.csv
cost_latency_params.csv
cost_partial.csv
```

These files report total parameters, trainable parameters, extra parameters, and inference latency.

### 9.6 Figures

```text
fig_clean_macro_f1_mean_sd.png
fig_macro_f1_missing_mean_sd.png
fig_macro_f1_corruption_mean_sd.png
fig_reliability_missing.png
fig_reliability_corruption.png
```

These figures correspond to the main robustness and reliability plots used in the manuscript.

---

## 10. Reviewer-Response Mapping

This repository directly supports reviewer verification of the revised manuscript.

### Reviewer Concern: Single split or single run

Addressed by:

```text
all_results_repeated_seed.csv
table_repeated_seed_mean_std_ci.csv
```

The experiments use three independent seeds: 42, 123, and 2025.

### Reviewer Concern: Lack of confidence intervals or significance analysis

Addressed by:

```text
table_repeated_seed_mean_std_ci.csv
table_paired_bootstrap_significance.csv
```

### Reviewer Concern: Non-monotonic metadata stress behavior

Addressed by deterministic nested metadata stress masks and degradation summaries:

```text
table_degradation_summary.csv
degradation_by_seed.csv
```

### Reviewer Concern: Reliability head collapse

Addressed by reliability anchoring and reliability verification:

```text
table_reliability_verification_summary.csv
reliability_verification_by_seed.csv
fig_reliability_missing.png
fig_reliability_corruption.png
```

### Reviewer Concern: No availability/plausibility ablation

Addressed by gate-mode ablation:

```text
table_rg_mf_inference_cue_ablation_summary.csv
table_rg_mf_inference_cue_ablation_by_seed.csv
```

### Reviewer Concern: No inference-cost indicator

Addressed by:

```text
table_cost_indicator_summary.csv
cost_latency_params.csv
```

### Reviewer Concern: Whether learned reliability estimation is better than a rule-based quality gate

Addressed by the direct rule-based (r^*) gate diagnostic baseline in the notebook and result tables.

### Reviewer Concern: Whether the model is image-dominated

Addressed by metadata-contribution diagnostics, zero-metadata gating, metadata-only analysis, and representation norm comparison in the notebook.

---

## 11. Reproducibility Notes

To reproduce the experiments as reported:

1. Use the same HAM10000 metadata file.
2. Use the same image directory structure.
3. Use the same random seeds: 42, 123, and 2025.
4. Use lesion-level grouped splitting based on `lesion_id`.
5. Use deterministic nested missingness and corruption masks.
6. Keep the same image size and preprocessing settings.
7. Run the notebook from top to bottom without changing model names or stress settings unless performing additional experiments.

Because GPU operations may involve nondeterministic backend behavior, very small numerical differences may occur across hardware or software environments.

---

## 12. Citation

If this repository supports your work, please cite the associated manuscript:

```bibtex
@article{furqan2026rgmf,
  title   = {Reliability-Gated Multimodal Fusion Algorithm for Teledermatology Under Missing and Corrupted Metadata},
  author  = {Furqan, Mhd. and Ab. Nasir, Ahmad Fakhri and Windarto, Agus Perdana and Harumy, T. H. F},
  journal = {IEEE Access},
  year    = {2026},
  note    = {Manuscript under review}
}
```

Please update the citation details after publication.

---

## 13. Authors

* Mhd. Furqan
  Universitas Islam Negeri Sumatera Utara, Indonesia

* Ahmad Fakhri Ab. Nasir
  Universiti Malaysia Pahang Al-Sultan Abdullah, Malaysia

* Agus Perdana Windarto
  STIKOM Tunas Bangsa, Indonesia

* T. H. F Harumy
  Universitas Sumatera Utara, Indonesia

---

## 14. Contact

For questions regarding the code or experiments, please contact:

**Mhd. Furqan**
Email: `mfurqan@uinsu.ac.id`

---

## 15. License

No license file has been specified in this repository at the time of writing. Please contact the corresponding author for reuse permissions. If the authors intend to make the code openly reusable, adding a formal license file such as MIT, Apache-2.0, or BSD-3-Clause is recommended.
