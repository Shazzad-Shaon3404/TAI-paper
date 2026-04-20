# YouCanCheck (YouCC): Trust-Aware Verification for Vulnerability Datasets

![Figure 1](https://drive.google.com/uc?export=view&id=1sBV-lEeP_lkicZ0kQhhf059qIHz2Oj_I)

## Table I. Comparison of Vulnerability Datasets on Evidence Artifacts and Data-Centric Cleaning

| Dataset / Work | Release Format | Commit Meta | Diff/Patch | Before/After Code | De-dup / Leakage | RAG Verify | Validity Score |
|---|---|---|---|---|---|---|---|
| BigVul | CSV (+JSON) | ✓ | ✓ | ✓ | NR | ✗ | ✗ |
| CVEfixes | Relational DB export | ✓ | ✓ | NR | ✗ | ✗ | ✗ |
| MegaVul | JSON | ✓ | ✓ | ✓ | ✗ | ✗ | ✗ |
| DiverseVul | Archive + metadata (Drive) | ✓ | ✓ | ✓ | ✗ | ✗ | ✗ |
| CrossVul | ~1.4GB archive (vuln+patched files) | ✓ | ✓ | NR | ✗ | ✗ | ✗ |
| VPP (Vulnerability Patch Pairs) | JSON | ✓ | ✓ | ✓ | NR | ✗ | ✗ |
| Devign (graph-based) | JSON graphs | ✗ | ✗ | ✗ | NR | ✗ | ✗ |
| ReVeal (slice-based) | Derived (program slices) | ✗ | ✗ | ✗ | NR | ✗ | ✗ |
| CleanVul (cleaned pairs) | CSV | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |

**Legend:** ✓ = yes, ✗ = no, NR = not reported


A research repository for **evidence-aware verification of vulnerability-labeled C/C++ datasets** using row-local retrieval, heuristic validity scoring, and downstream model evaluation.
## Table II. Dataset Subsets Utilized in the Current Study

| Dataset | Language | Subset Size | Train (80%) | Test (20%) |
|---|---|---:|---:|---:|
| MegaVul | C/C++ | 2,000 | 1,600 | 400 |
| CVEFixes | C/C++ | 10,000 | 8,000 | 2,000 |
| DiverseVul | C/C++ | 10,000 | 8,000 | 2,000 |
---

## Overview

Public vulnerability datasets are widely used to train and evaluate machine learning and large language model based vulnerability detectors. However, many benchmark labels are derived indirectly from security commits, CVE/CWE mappings, and repository metadata, which may not always provide strong evidence that a code sample is truly vulnerable.

**YouCanCheck (YouCC)** is a retrieval-augmented verification framework designed to assess whether vulnerability-labeled samples are actually supported by evidence. Instead of trusting the dataset label directly, YouCC constructs a row-local evidence pack for each sample, retrieves the most relevant contextual evidence, and produces a structured verdict such as:

- **Verified**
- **Uncertain**
- **Contradicted**

This repository contains the workflow, models, evaluation logic, and result organization for the YouCC framework.

![Figure 2](https://drive.google.com/uc?export=view&id=1Jxa6RC3Jua7QwhC3UGeaurUOe_LlLP00)



---

## Motivation

In vulnerability detection research, model performance is often reported without first checking whether the dataset labels themselves are strongly supported. This can lead to misleading conclusions, especially when labels come from indirect artifacts such as:

- security commits
- CVE/CWE mappings
- repository metadata
- patch-linked heuristics

YouCC is designed to shift the focus from **benchmark consumption** to **benchmark verification**.


## Table III. Verdict Distribution Across MegaVul, CVEFixes, and DiverseVul

| Dataset | Uncertain Count | Uncertain (%) | Verified Count | Verified (%) | Contradicted Count | Contradicted (%) |
|---|---:|---:|---:|---:|---:|---:|
| MegaVul | 1,514 | 62.23 | 918 | 37.73 | 1 | 0.04 |
| CVEFixes | 7,117 | 71.17 | 2,878 | 28.78 | 5 | 0.05 |
| DiverseVul | 957 | 9.57 | 9,043 | 90.43 | 0 | 0.00 |

![Figure 3](https://drive.google.com/uc?export=view&id=1J1QIpmcPtGELBZx-kDAq7zvD_OS3H3Xb)

## Table IV. Derived Reliability and Review Metrics Across Datasets

| Dataset | Total | RB (%) | VSI | NG (pp) | Non-C (%) |
|---|---:|---:|---:|---:|---:|
| MegaVul | 2,433 | 62.27 | 0.61 | -24.50 | 99.96 |
| CVEFixes | 10,000 | 71.22 | 0.40 | -42.39 | 99.95 |
| DiverseVul | 10,000 | 9.57 | 9.45 | 80.86 | 100.00 |

![Figure 4](https://drive.google.com/uc?export=view&id=1rPJnBmstgWGjrYhHupDmcCePEsIKauPp)
<details>
<summary><b>Table V. Comparison of Original and Verified Views Across Datasets</b></summary>

| Dataset | Model | View | Acc | Pre | Rec | F1 | MCC | AUC |
|---|---|---|---:|---:|---:|---:|---:|---:|
| MegaVul | QwenCoder | Original | 0.8472 | 0.8349 | 0.8655 | 0.8499 | 0.6948 | 0.9148 |
| MegaVul | QwenCoder | Verified | 0.8319 | 0.8212 | 0.8533 | 0.8369 | 0.6641 | 0.9039 |
| MegaVul | DeepSeek | Original | 0.8178 | 0.8009 | 0.8460 | 0.8228 | 0.6367 | 0.8983 |
| MegaVul | DeepSeek | Verified | 0.8257 | 0.8146 | 0.8484 | 0.8311 | 0.6518 | 0.8970 |
| MegaVul | UnixCoder | Original | 0.7852 | 0.7945 | 0.8142 | 0.7245 | 0.5012 | 0.7521 |
| MegaVul | UnixCoder | Verified | 0.8208 | 0.8188 | 0.8289 | 0.8238 | 0.6415 | 0.8874 |
| MegaVul | Phi-2B | Original | 0.8020 | 0.7947 | 0.8142 | 0.8043 | 0.6041 | 0.8868 |
| MegaVul | Phi-2B | Verified | 0.8096 | 0.8000 | 0.8313 | 0.8153 | 0.6196 | 0.8817 |
| MegaVul | GraphCodeBERT | Original | 0.7512 | 0.7451 | 0.7456 | 0.7785 | 0.4584 | 0.7745 |
| MegaVul | GraphCodeBERT | Verified | 0.7555 | 0.7751 | 0.8000 | 0.7945 | 0.5012 | 0.7542 |
| CVEFixes | QwenCoder | Original | 0.6845 | 0.6458 | 0.7227 | 0.6150 | 0.3584 | 0.8000 |
| CVEFixes | QwenCoder | Verified | 0.8178 | 0.8009 | 0.8460 | 0.8228 | 0.7897 | 0.8983 |
| CVEFixes | DeepSeek | Original | 0.7080 | 0.6646 | 0.6993 | 0.6815 | 0.4135 | 0.7757 |
| CVEFixes | DeepSeek | Verified | 0.7745 | 0.7510 | 0.7418 | 0.7464 | 0.5435 | 0.8010 |
| CVEFixes | UnixCoder | Original | 0.6676 | 0.6010 | 0.7582 | 0.6705 | 0.3536 | 0.7470 |
| CVEFixes | UnixCoder | Verified | 0.6854 | 0.6125 | 0.7745 | 0.6715 | 0.3545 | 0.7493 |
| CVEFixes | Phi-2B | Original | 0.7434 | 0.6946 | 0.7582 | 0.7250 | 0.4871 | 0.7780 |
| CVEFixes | Phi-2B | Verified | 0.7585 | 0.7012 | 0.7500 | 0.7252 | 0.5012 | 0.7412 |
| CVEFixes | GraphCodeBERT | Original | 0.6793 | 0.6138 | 0.7582 | 0.6784 | 0.3737 | 0.7377 |
| CVEFixes | GraphCodeBERT | Verified | 0.6878 | 0.6200 | 0.7745 | 0.7945 | 0.4112 | 0.7542 |
| DiverseVul | QwenCoder | Original | 0.6045 | 0.6258 | 0.7427 | 0.5950 | 0.3784 | 0.7800 |
| DiverseVul | QwenCoder | Verified | 0.7978 | 0.8209 | 0.8260 | 0.8428 | 0.7697 | 0.9183 |
| DiverseVul | DeepSeek | Original | 0.6880 | 0.6846 | 0.6793 | 0.7015 | 0.3935 | 0.7957 |
| DiverseVul | DeepSeek | Verified | 0.7945 | 0.7310 | 0.7618 | 0.7264 | 0.5635 | 0.7810 |
| DiverseVul | UnixCoder | Original | 0.6476 | 0.6210 | 0.7382 | 0.6905 | 0.3336 | 0.7670 |
| DiverseVul | UnixCoder | Verified | 0.7054 | 0.5925 | 0.7945 | 0.6515 | 0.3745 | 0.7293 |
| DiverseVul | Phi-2B | Original | 0.7234 | 0.7146 | 0.7382 | 0.7450 | 0.4671 | 0.7980 |
| DiverseVul | Phi-2B | Verified | 0.7785 | 0.6812 | 0.7700 | 0.7052 | 0.5212 | 0.7212 |
| DiverseVul | GraphCodeBERT | Original | 0.6993 | 0.5938 | 0.7782 | 0.6584 | 0.3937 | 0.7177 |
| DiverseVul | GraphCodeBERT | Verified | 0.6678 | 0.6400 | 0.7545 | 0.7745 | 0.4312 | 0.7342 |

</details>


## Table VI. Percentage Change from Original to Verified View

| Dataset | Model | ΔAcc (%) | ΔF1 (%) | ΔMCC (%) | ΔAUC (%) |
|---|---|---:|---:|---:|---:|
| MegaVul | QwenCoder | -1.81 | -1.53 | -4.42 | -1.19 |
| MegaVul | DeepSeek | 0.97 | 1.01 | 2.37 | -0.14 |
| MegaVul | UnixCoder | 4.53 | 13.71 | 27.99 | 17.99 |
| MegaVul | Phi-2B | 0.95 | 1.37 | 2.57 | -0.58 |
| MegaVul | GraphCodeBERT | 0.57 | 2.06 | 9.34 | -2.62 |
| CVEFixes | QwenCoder | 19.47 | 33.79 | 120.34 | 12.29 |
| CVEFixes | DeepSeek | 9.39 | 9.52 | 31.44 | 3.26 |
| CVEFixes | UnixCoder | 2.67 | 0.15 | 0.25 | 0.31 |
| CVEFixes | Phi-2B | 2.03 | 0.03 | 2.89 | -4.73 |
| CVEFixes | GraphCodeBERT | 1.25 | 17.11 | 10.03 | 2.24 |
| DiverseVul | QwenCoder | 31.98 | 41.65 | 103.41 | 17.73 |
| DiverseVul | DeepSeek | 15.48 | 3.55 | 43.20 | -1.85 |
| DiverseVul | UnixCoder | 8.93 | -5.65 | 12.26 | -4.92 |
| DiverseVul | Phi-2B | 7.62 | -5.34 | 11.58 | -9.62 |
| DiverseVul | GraphCodeBERT | -4.50 | 17.63 | 9.53 | 2.30 |
---

## Main Contributions

This work makes three main contributions:

1. **Evidence Pack Construction**  
   Builds sample-level evidence packs using commit messages, diff hunks, and before/after contextual artifacts.

2. **RAG-Based Verification**  
   Uses row-local lexical retrieval and heuristic scoring to evaluate whether the evidence supports the vulnerability claim.

3. **Verification-to-Identification Comparison**  
   Compares model performance on the **original** dataset view and the **verification-filtered** dataset view.

---

## Workflow

The full pipeline contains three major stages:

1. **Sample collection and preprocessing**
2. **YouCC verification using row-local retrieval**
3. **Evaluation of original vs. verified dataset views with multiple code models**

### Overview





