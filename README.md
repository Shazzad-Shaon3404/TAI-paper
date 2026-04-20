# YouCanCheck (YouCC): Trust-Aware Verification for Vulnerability Datasets

A research repository for **evidence-aware verification of vulnerability-labeled C/C++ datasets** using row-local retrieval, heuristic validity scoring, and downstream model evaluation.

---

## Overview

Public vulnerability datasets are widely used to train and evaluate machine learning and large language model based vulnerability detectors. However, many benchmark labels are derived indirectly from security commits, CVE/CWE mappings, and repository metadata, which may not always provide strong evidence that a code sample is truly vulnerable.

**YouCanCheck (YouCC)** is a retrieval-augmented verification framework designed to assess whether vulnerability-labeled samples are actually supported by evidence. Instead of trusting the dataset label directly, YouCC constructs a row-local evidence pack for each sample, retrieves the most relevant contextual evidence, and produces a structured verdict such as:

- **Verified**
- **Uncertain**
- **Contradicted**

This repository contains the workflow, models, evaluation logic, and result organization for the YouCC framework.

---

## Motivation

In vulnerability detection research, model performance is often reported without first checking whether the dataset labels themselves are strongly supported. This can lead to misleading conclusions, especially when labels come from indirect artifacts such as:

- security commits
- CVE/CWE mappings
- repository metadata
- patch-linked heuristics

YouCC is designed to shift the focus from **benchmark consumption** to **benchmark verification**.

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

### Graphical Overview

```html
<h2>Graphical Overview</h2>
<p align="center">
  <img src="results/figures/graphical_overview.png" alt="YouCC Workflow" width="1000">
</p>
