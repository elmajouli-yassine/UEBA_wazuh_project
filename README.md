# 🛡️ UEBA_wazuh_project

> **User and Entity Behavior Analytics (UEBA) prototype built on top of Wazuh**

A security analytics system that ingests Wazuh alerts and logs, applies machine learning-based anomaly detection and behavioral baselining, and generates high-risk alerts for suspicious user or host activity.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Repository Structure](#repository-structure)
- [Dataset](#dataset)
- [Machine Learning Models](#machine-learning-models)
- [Getting Started](#getting-started)
- [Prerequisites](#prerequisites)
- [Usage](#usage)
- [Results & Evaluation](#results--evaluation)
- [Integration with Wazuh](#integration-with-wazuh)
- [Contributing](#contributing)

---

## Overview

UEBA_wazuh_project extends the open-source [Wazuh](https://wazuh.com/) SIEM/XDR platform with a machine learning layer that models normal user and host behavior over time. By comparing incoming events against established behavioral baselines, the system flags deviations that may indicate insider threats, compromised accounts, lateral movement, or other anomalous activity — risks that signature-based rules alone cannot reliably detect.

---

## Features

- **Behavioral Baselining** — establishes per-user and per-host activity profiles from historical Wazuh logs
- **Anomaly Detection** — detects deviations from baseline using supervised ML models
- **Multi-class / Binary Classification** — labels events as normal or high-risk (2-label classification)
- **Random Forest & Gradient Boosting** — two ensemble models trained and evaluated side-by-side
- **Wazuh-native Dataset** — training data derived from real Wazuh alert fields and log attributes
- **Alert Generation** — outputs high-risk alerts for suspicious user or entity activity

---

## Architecture

```
Wazuh Agents
     │  (logs, alerts, syscheck, auditd, etc.)
     ▼
Wazuh Manager / Indexer
     │  (normalized alerts via ossec.log / Elasticsearch API)
     ▼
UEBA Pipeline (Python / Jupyter)
  ├── Data Ingestion      ← ueba_dataset_2label.csv
  ├── Feature Engineering ← behavioral features per user/host
  ├── Model Training      ← Random Forest + Gradient Boosting
  ├── Inference           ← score incoming events
  └── Alert Output        ← high-risk events flagged for SOC
```

---

## Repository Structure

```
UEBA_wazuh_project/
├── randomforest_&_gradient_ml.ipynb   # Main ML notebook (training, evaluation, inference)
├── ueba_dataset_2label.csv            # Labeled UEBA dataset (normal / malicious)
├── wazuh-ml_CSCC-13 (7).pptx         # Project presentation slides
└── README.md
```

---

## Dataset

The dataset `ueba_dataset_2label.csv` contains labeled log records extracted from Wazuh, formatted for binary classification:

| Label | Meaning |
|-------|---------|
| `0`   | Normal / benign activity |
| `1`   | Suspicious / malicious activity |

Features are derived from Wazuh alert fields including event severity, rule IDs, source/destination metadata, authentication events, and file integrity monitoring data. The 2-label format makes it suitable for training a binary anomaly classifier.

---

## Machine Learning Models

The notebook `randomforest_&_gradient_ml.ipynb` implements and compares two ensemble classifiers:

### Random Forest
- Ensemble of decision trees trained on bootstrap samples
- Resistant to overfitting, handles feature interactions well
- Provides feature importance rankings

### Gradient Boosting
- Sequential ensemble that corrects prior model errors
- Generally higher accuracy on tabular security data
- Configurable learning rate and tree depth

Both models are evaluated using standard classification metrics: accuracy, precision, recall, F1-score, and confusion matrix.

---

## Getting Started

### Prerequisites

- Python 3.8+
- Jupyter Notebook or JupyterLab
- A running Wazuh instance (optional — for live data ingestion)

Install required Python packages:

```bash
pip install pandas numpy scikit-learn matplotlib seaborn jupyter
```

### Usage

1. **Clone the repository**

```bash
git clone https://github.com/elmajouli-yassine/UEBA_wazuh_project.git
cd UEBA_wazuh_project
```

2. **Launch the notebook**

```bash
jupyter notebook "randomforest_&_gradient_ml.ipynb"
```

3. **Run all cells** to:
   - Load and explore `ueba_dataset_2label.csv`
   - Preprocess and engineer features
   - Train Random Forest and Gradient Boosting models
   - Evaluate model performance
   - Score new events for anomaly detection

4. **Plug in your own data** by replacing or appending rows to `ueba_dataset_2label.csv` with Wazuh alert exports in the same schema.

---

## Results & Evaluation

The notebook produces evaluation outputs including:

- Confusion matrix for each model
- Classification report (precision / recall / F1 per class)
- Feature importance chart (which Wazuh fields contribute most to anomaly scoring)
- Comparative accuracy between Random Forest and Gradient Boosting

---

## Integration with Wazuh

To feed live Wazuh alerts into the UEBA pipeline:

1. Export alerts from the Wazuh Indexer (Elasticsearch/OpenSearch) using the REST API or Kibana/Dashboard export.
2. Normalize the exported JSON to match the CSV schema used in this project.
3. Run inference using the trained model to score each event.
4. Events with a predicted label of `1` can be forwarded back to Wazuh as custom alerts, or integrated with a SOAR platform (e.g., TheHive / Shuffle) for automated case creation.

---

## Contributing

Contributions, issues, and feature requests are welcome. Feel free to open an issue or submit a pull request.


---

*Built with ❤️ on top of [Wazuh](https://wazuh.com/) — the open-source security platform.*
