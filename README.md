# Cloud Intrusion Detection under Distribution Shift

## Evaluating Graph, Temporal, and Anomaly Learning for Cloud Intrusion Detection under Distribution Shift

This repository contains the code, experiment specifications, reproducibility
documentation, and derived results supporting the manuscript:

**“Evaluating Graph, Temporal, and Anomaly Learning for Cloud Intrusion Detection under Distribution Shift.”**

The study evaluates whether increasingly complex intrusion-detection approaches
provide reproducible incremental value under chronological and attack-family
distribution shift. The experiments compare conventional supervised flow
classifiers, endpoint graph learning, explicit temporal context, benign-trained
reconstruction, and simple decision-level fusion on the CSE-CIC-IDS2018 dataset.

Rather than proposing a single integrated architecture, the study evaluates
individual components using matched controls, frozen experimental protocols,
validation-selected operating points, and five fixed training seeds.

---

## Study Overview

The experimental design addresses six research questions:

- **RQ1:** How do supervised flow-based classifiers perform under chronological
  train, validation, and test partitions?
- **RQ2:** How well do frozen models detect attack families absent from the
  training partition, specifically Bot and Infilteration?
- **RQ3:** What incremental contribution does relational message passing provide
  over matched edge-only and self-only controls on the February 20 endpoint-graph
  benchmark?
- **RQ4:** Does longer explicit temporal context provide incremental predictive
  value over matched minimal-context controls?
- **RQ5:** Does benign-trained reconstruction provide complementary detection
  behavior relative to the supervised baseline, and does simple RF-AE fusion
  improve detection without an unfavorable false-positive tradeoff?
- **RQ6:** What computational overhead accompanies the evaluated model
  components?

Three principles guide the evaluation:

1. Threshold-free ranking metrics and thresholded operating-point metrics are
   reported separately.
2. Component contributions are evaluated using matched controls and within-seed
   differences rather than inferred from absolute performance alone.
3. Variation across five fixed seeds represents training stochasticity on a
   fixed dataset split, not uncertainty across independently sampled datasets.

---

## Dataset

The study uses the **CSE-CIC-IDS2018** intrusion-detection benchmark.

The experimental inventory contains ten processed machine-learning CSV files
used for the principal flow experiments. Before cleaning, these files contain
approximately **16.23 million records**, including approximately **13.48 million
benign** and **2.75 million malicious** records.

The original dataset is not redistributed through this repository.

The data-quality audit identified:

| Finding | Result |
|---|---:|
| Raw records | 16,233,002 |
| Explicit NaN cells | 59,721 |
| Infinite cells | 131,799 |
| Repeated-header records | 59 |
| Non-header exact duplicates removed | 410,707 |
| Year-1970 timestamp anomalies | 14 |

Infinite values are converted to missing values, affected numerical features are
imputed using training-fitted statistics, repeated headers and exact duplicates
are removed, and the 14 anomalous year-1970 records are quarantined from
temporal experiments.

The dataset spelling **`Infilteration`** is intentionally preserved throughout
the project.

---

## Experimental Cohorts

The study deliberately separates chronological generalization from graph and
temporal component evaluation.

| Cohort | Train / Validation / Test | Purpose |
|---|---|---|
| **Full chronological dataset** | 12,795,136 / 1,652,943 / 1,374,143 | Supervised flow models, anomaly detection, RF-AE fusion, and later-period attack-family analysis |
| **Phase-5 matched temporal cohort** | 99,962 / 206,603 / 171,753 | Transformer-L64 versus capacity-matched Transformer-L1 |
| **February-20 matched graph cohort** | 172,984 / 88,782 / 212,591 | GATv2, edge-MLP, self-only GAT, and graph-temporal comparisons |

Results from these cohorts should **not** be interpreted as interchangeable
component ablations.

---

## Chronological Full-Data Split

The principal flow experiment preserves chronological ordering.

| Partition | Dates | Benign | Malicious | Total |
|---|---|---:|---:|---:|
| Train | Feb. 14–22, 2018 | 10,885,643 | 1,909,493 | 12,795,136 |
| Validation | Feb. 23 & 28, 2018 | 1,583,520 | 69,423 | 1,652,943 |
| Test | Mar. 1–2, 2018 | 998,793 | 375,350 | 1,374,143 |

All malicious test records belong to two attack families absent from training:

- **Bot:** 282,310 test records
- **Infilteration:** 93,040 test records

Bot is absent from both training and validation.

Infilteration is absent from training but appears in validation. It is therefore
**training-unseen**, but not fully development-held-out.

The study does not characterize these experiments as real-world zero-day
detection.

---

## Evaluated Models

### Supervised Flow Models

The full-data supervised evaluation includes:

- Logistic Regression (LR)
- Random Forest (RF)
- XGBoost (XGB)
- Multilayer Perceptron (MLP)

All final models are evaluated using five fixed training seeds:

```text
42, 123, 456, 789, 1024
