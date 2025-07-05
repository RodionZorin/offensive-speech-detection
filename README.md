## Offensive Language Detection Pipeline

A pipeline for fine-tuning and evaluating transformer-based models on the **Offensive Language Identification (OLID)** task.
Supports **English Tasks A, B, C** and **Danish Task A**.

### Table of Contents

- [Project Overview](#project-overview)
- [Setup](#setup)
- [Repository Structure](#repository-structure)
- [Code Architecture](#code-architecture)
  - [`training.py` – Fine-Tuning Models](#trainingpy--fine-tuning-models)
  - [`evaluation.py` – Model Evaluation](#evaluationpy--model-evaluation)
- [Features](#features)
- [Citation](#citation)

#### Project Overview

This repository supports fine-tuning and evaluation for the following tasks:

| Subtask | Description |
|:-:|:-|
| **Task A (English)** | Offensive vs. Non-Offensive classification |
| **Task B (English)** | Targeted vs. Untargeted offense classification |
| **Task C (English)** | Target Type: Individual, Group, Other |
| **Task A (Danish)** | Offensive vs. Non-Offensive classification (Multilingual Dataset) |

#### Setup

Before running any scripts, install the required dependencies:

```bash
pip install -r requirements.txt
```

#### Repository Structure

```
├── data/
├── src/
│   ├── training.py
│   ├── evaluation.py
│   ├── preprocessing.py
│   └── utils.py
├── .gitlab-ci.yml
├── LICENSE
├── README.md
└── requirements.txt            
```

#### Code Architecture

The project consists of two main scripts:

##### `training.py` – Fine-Tuning Models

This script handles **model training** for all supported tasks. You can customize:

- **The subtask:** Task A/B/C in English or Task A in Danish
- **Model architecture:** e.g., `bert-base-cased`, `albert-base-v2`, `bert-base-multilingual-cased`
- **Preprocessing methods:** If no method is specified, the default is no preprocessing
- **Dataset size:** Default is a sample of **1000 tweets**; you can train on the **full dataset**
- **Hyperparameters:** Batch size, learning rate, epochs
  *(Default settings: `batch size = 8`, `learning rate = 2e-5`, `epochs = 3`)*
  **Note:** Only one hyperparameter may be changed at a time while keeping others at default.

After training, the model is **saved and evaluated** on the test set automatically.

**Usage Example:**
```bash
cd src
python training.py --task A-eng --model albert-base-v2 --preprocessing-data lowercasing --hyperparameters batch-16 --full-dataset
```

###### Command-Line Options

| Argument | Description | Choices / Examples |
|:-:|:-|:-|
| `-h, --help` | Show help message and exit | |
| `--task` | Choose the task to fine-tune the model on | `{A-eng, B-eng, C-eng, A-danish}` |
| `--model` | Specify the model architecture | `{bert-base-cased, albert-base-v2, bert-base-multilingual-cased}` |
| `--hyperparameters` | Set one or more hyperparameters | `{batch-1, batch-16, epochs-6, lr-1e-5, lr-3e-5}`<br>*(Default: batch_size=8, epochs=3, lr=2e-5)* |
| `--full-dataset` | Train on the full dataset instead of default subset | *(Flag, no value required)* |
| `--preprocessing-data` | Specify one or more preprocessing techniques | `{remove-hashtags-urls-at, lowercasing, expand-emojis, remove-stopwords, stemming}` |

##### `evaluation.py` – Model Evaluation

This script loads **saved fine-tuned models** from the `finetuned_offensive` directory and evaluates them on the test data. It supports **all four subtasks** and reports:

- **Macro F1-Score** (Primary metric)
- **Accuracy**

For each subtask, we represent one model that is the **best** for that subtask (in terms of F1).
Thus, there are four best models available (one for each subtask).

**Usage Example:**
```bash
python evaluation.py --model bert-base-cased-task-A-eng
```

###### Command-Line Options

| Argument | Description | Choices |
|:-:|:-|:-|
| `-h, --help` | Show help message and exit | |
| `--model` | Choose one of the pre-trained models to evaluate | `{bert-base-cased-task-A-eng, bert-base-cased-task-B-eng, bert-base-cased-task-C-eng, bert-base-multilingual-cased-task-A-danish}` |
'bert-base-cased-task-A-eng', 'bert-base-cased-task-B-eng', 'bert-base-cased-task-C-eng', 'bert-base-multilingual-cased-task-A-danish'

###### Results of our best models
bert-base-cased-task-A-eng: MacroAvg F1 score **0.8175**
bert-base-cased-task-B-eng: MacroAvg F1 score **0.7318**
bert-base-cased-task-C-eng: MacroAvg F1 score **0.5773**
bert-base-multilingual-cased-task-A-danish: MacroAvg F1 score **0.7814**

OffensEval 2020 reports the following majority baselines for the tasks (F1-score):
• Subtask A (English): 0.4193
• Subtask B (English): 0.3741
• Subtask C (English): 0.2704
• Subtask A (Danish): 0.4668

#### Features

Fine-tuning & Evaluation for **4 subtasks**
Multiple **Transformer architectures** supported
Configurable **Preprocessing pipeline**
Flexible **Hyperparameter tuning**
Supports **English & Danish datasets**
Evaluation metrics: **Macro F1-Score & Accuracy**
Best fine-tuned models available in `finetuned_offensive/`

#### Citation

If you use this repository for your research or coursework, please consider citing:

> Zampieri et al. (2020). SemEval-2020 Task 12: Multilingual Offensive Language Identification in Social Media (OffensEval 2020). In *Proceedings of the Fourteenth Workshop on Semantic Evaluation (SemEval-2020)*.
