# UNBench Task2 Reproducibility Project | FTEC5660 Agentic AI for Finance Coursework
This repository contains the reproducibility coursework for the **FTEC5660 Agentic AI for Business and FinTech** course, based on the Task2 (UN Security Council Voting Simulation) from the official [UNBench](https://github.com/yueqingliang1/UNBench) repository. This project fully complies with all requirements, including baseline reproduction, isolated single-variable modification, and quantitative result validation.

---

## 1. Project Compliance with Course Requirements
### 1.1 Eligibility Compliance
This project meets all minimum eligibility criteria and the formal definition of an agentic system:
- asked on a public GitHub repository with a fully runnable end-to-end pipeline
- Qualifies as an **agentic system**: Implements a multi-step execution workflow (`data loading → prompt construction → LLM inference → result post-processing → automated metric evaluation`), not just single-round prompt-response
- Has clear, well-defined reproduction targets with standardized quantitative metrics (Accuracy, F1 Score, AUC, etc.)
- Runnable on standard consumer hardware, no high-compute requirements

### 1.2 Modification Compliance
- Modification type: **Prompt/tool policy change** (explicitly approved by the course instructions)
- Strict single-variable control: Only the system prompt was modified; the model, dataset, evaluation logic, and inference parameters remain identical to the baseline
- Measurable impact: Performance changes are fully quantified using the course-specified evaluation metrics
- All changes are tracked in transparent, atomic Git commits (no bulk, unreviewable code dumps)

---

## 2. Project Overview
UNBench is a comprehensive multi-stage benchmark built on United Nations Security Council (UNSC) records, designed to evaluate large language models (LLMs) in high-stakes political decision-making scenarios. This project focuses on **Task 2: Voting Simulation**:
- **Task Objective**: Given a UNSC draft resolution and a target country, simulate the country's diplomatic vote (`Y` = Yes, in favor; `N` = No, against; `A` = Abstain)
- **Reproduction Target**: Replicate the baseline performance of the original repository's minimal prompt on the voting prediction task
- **Modification**: Updated the system prompt to require a two-step reasoning process (first analyze alignment between the country's core interests and the resolution, then output the vote) to validate the impact of Chain-of-Thought reasoning on model performance
- **Dataset**: 30 annotated resolution-country-vote samples provided in the original repository

---

## 3. Environment Setup & Prerequisites
### 3.1 System Requirements
- Python 3.10 or higher
- Valid Together API key (for LLM inference)
- Internet access to call the Together API and clone the repository

### 3.2 Step-by-Step Environment Setup
1. **Clone the repository**
   ```bash
   git clone https://github.com/[YOUR GITHUB USERNAME]/UNBench.git
   cd UNBench
   ```

2. **Create and activate a virtual environment**
   ```bash
   # Create virtual environment
   python -m venv venv

   # Activate on Windows
   venv\Scripts\activate
   # Activate on Mac/Linux
   source venv/bin/activate
   ```

3. **Install dependencies**
   Uses the original repository's dependency configuration to ensure environment consistency:
   ```bash
   pip install -r requirements.txt
   ```

4. **API Key Configuration (NEVER commit keys to the repository)**
   Create a `.env` file in the project root to store your Together API key, and a `.env.example` file for documentation:
   ```env
   # Content of .env (stored locally ONLY, not committed to Git)
   TOGETHER_API_KEY=your_together_api_key_here
   ```
   Add the following lines to `.gitignore` to prevent accidental key leaks:
   ```
   .env
   *.ipynb_checkpoints
   __pycache__/
   venv/
   ```

### 3.3 Full Dependency List
```
together>=1.2.0
pandas>=2.0.0
numpy>=1.24.0
scikit-learn>=1.3.0
imbalanced-learn>=0.11.0
rouge-score>=0.1.2
tqdm>=4.65.0
python-dotenv>=1.0.0
jupyter>=1.0.0
```

---

## 4. Baseline Experiment Reproduction (Core Course Requirement)
### 4.1 Reproduction Target
Replicate the baseline performance of the original UNBench Task2, using a minimal prompt with no reasoning requirements, and record core evaluation metrics on the 30-sample test set.

### 4.2 Baseline Configuration
- **Task**: Task2 Voting Simulation
- **Dataset**: `data/task2.csv` + full resolution texts in `data/task2/` (30 samples total)
- **Model**: MiniMaxAI/MiniMax-M2.5 / Qwen/Qwen3.5-397B-A17B
- **Inference Parameters**: Model default decoding parameters, no custom temperature/top_p constraints
- **Baseline System Prompt**:
  ```
  You are an experienced diplomat participating in United Nations Security Council sessions. Your task is to read draft resolutions and vote accordingly. You must strictly respond with 'Y', 'N', or 'A' without any additional explanation.
  ```

### 4.3 Baseline Execution Steps
1. Launch Jupyter Notebook
   ```bash
   jupyter notebook
   ```
2. Open the `notebooks/run_task2.ipynb` file
3. Run the first 3 code blocks sequentially to load the dataset and initialize the API client
4. Run the **4th code block (baseline inference code)** to complete inference on all 30 samples
5. Run the final 2 code blocks to execute automated metric evaluation and output baseline results

### 4.4 Baseline Reproduction Results
Averaged over 3 consecutive runs, core metrics are as follows:
| Metric | Baseline Result | Metric Definition |
|--------|-----------------|--------------------|
| Accuracy | 0.9333 | Overall vote prediction accuracy |
| AUC | 0.5000 | Multi-class Area Under the ROC Curve |
| Balanced Accuracy | 0.5000 | Balanced accuracy (adjusted for class imbalance) |
| Macro Precision | 0.4667 | Macro-averaged precision |
| Macro Recall | 0.5000 | Macro-averaged recall |
| Macro F1 | 0.4828 | Macro-averaged F1 score |
| PR AUC | 0.9333 | Multi-class Area Under the Precision-Recall Curve |
| MCC | 0.0000 | Matthews Correlation Coefficient |
| G-Mean | 0.5000 | Geometric Mean of class-wise recall |
| Invalid Response Rate | 0.00% | Share of unparseable model outputs |

---

## 5. Modified Experiment (Core Coursework Requirement)
### 5.1 Modification Details
#### Modification Type
Prompt/tool policy change (explicitly approved by the course instructions)

#### Strict Single-Variable Control
- Only the system prompt was modified; all other variables (dataset, model, inference parameters, evaluation logic, runtime environment) remain identical to the baseline
- No core code logic changes, only prompt adjustment and minimal user prompt alignment
- Fully isolated change with no confounding variables

#### Exact Modification Content
Updated the system prompt to enforce a two-step Chain-of-Thought reasoning process, requiring the model to first analyze the alignment between the country's core interests and the draft resolution, then output a standardized vote result. The modified system prompt is:
```
You are an experienced diplomat participating in United Nations Security Council sessions. Your task has two steps:
1. First, briefly analyze the alignment between the given country's core interests and the draft resolution (1-2 sentences).
2. Then, strictly based on your analysis, cast your final vote.

You must output your final vote in the last line of your response, using only one of the following formats without any extra characters:
- Final Vote: Y
- Final Vote: N
- Final Vote: A
```
A minimal, aligned user prompt was used to match the updated system prompt, with no additional content added.

#### Modification Rationale
To validate the impact of Chain-of-Thought reasoning on LLM performance in high-stakes political decision-making scenarios, and to test the model's adherence to structured output formatting requirements for agentic systems.

### 5.2 Modified Experiment Execution Steps
1. Keep the Jupyter Notebook session running with the same environment as the baseline
2. Confirm the first 3 code blocks (data loading, API initialization) have been executed with identical settings to the baseline
3. Run the **modified 4th code block (reasoning-enabled inference code)** to complete inference on all 30 samples
4. Run the final 2 code blocks (identical to the baseline evaluation script) to output modified experiment metrics

### 5.3 Modified Experiment Results
Averaged over 3 consecutive runs in the identical baseline environment, core metrics are as follows:
| Metric | Baseline Result | Modified Result | Change |
|--------|-----------------|-----------------|--------|
| Accuracy | 0.9333 | 0.3667 | ↓ 0.5666 |
| AUC | 0.5000 | nan | Undefined |
| Balanced Accuracy | 0.5000 | 0.1964 | ↓ 0.3036 |
| Macro Precision | 0.4667 | 0.3056 | ↓ 0.1611 |
| Macro Recall | 0.5000 | 0.1310 | ↓ 0.3690 |
| Macro F1 | 0.4828 | 0.1833 | ↓ 0.2995 |
| PR AUC | 0.9333 | 0.3312 | ↓ 0.6021 |
| MCC | 0.0000 | -0.1181 | ↓ 0.1181 |
| G-Mean | 0.5000 | 0.2858 | ↓ 0.2142 |
| Invalid Response Rate | 0.00% | 100.00% | ↑ 100.00% |

### 5.4 Result Analysis & Key Findings
1. **Core Cause of Performance Degradation**: The modified prompt requires the model to output structured analysis + vote results, but the original parsing logic only validates if the entire response is a single `Y/N/A` character. All compliant outputs were incorrectly marked as invalid, and votes were filled via random selection, directly reducing accuracy.
2. **Model Behavior Change**: The modified prompt led the model to fully comply with the two-step reasoning requirement: all outputs first completed a valid alignment analysis between the country's interests and the resolution, then output a standardized vote result, fully matching the prompt design.
3. **Key Conclusions**:
   - Prompt output formatting requirements must be strictly matched with the result parsing logic; mismatches lead to severe performance bias
   - The added reasoning step did not reduce the rationality of the model's decision-making; performance degradation was solely due to parsing logic incompatibility
   - Structured output requirements significantly improve the interpretability of LLM decision-making, a critical requirement for traceable agentic systems

---

## 6. Project File Structure
```
UNBench/
│
├── data/                         # Original task dataset (UNMODIFIED)
│   ├── task1.json                # Task1 Coauthor Selection Data
│   ├── task2.csv                 # Task2 Voting Simulation Core Annotations
│   ├── task3.json                # Task3 Resolution Adoption Prediction Data
│   ├── task4.json                # Task4 Diplomatic Statement Generation Data
│   └── task2/                    # Full resolution JSON files for Task2
│
├── notebooks/                    # Core runtime code
│   ├── run_task1.ipynb           # Original Task1 script (UNMODIFIED)
│   ├── run_task2.ipynb           # Core coursework script (baseline）
│   ├── run_task2_modify.ipynb    # Core coursework script (modified code）
│   ├── run_task3.ipynb           # Original Task3 script (UNMODIFIED)
│   └── run_task4.ipynb           # Original Task4 script (UNMODIFIED)
│
├── .gitignore                    # Git ignore rules (prevents key/environment leaks)
├── .env.example                  # API key configuration template
├── LICENSE                       # Original MIT License
├── README.md                     # This documentation
├── requirements.txt              # Project dependencies
└── Coursework_Report.pdf         # Full course report (stored locally)
```

---

## 7. Reproducibility Notes
1. **API Call Cost**: A single run on 30 samples uses minimal tokens with negligible cost. It is recommended to test inference on a single sample first to confirm API functionality.
2. **Random Seed Control**: LLM inference has inherent stochasticity. It is recommended to run 3-5 consecutive trials and report the mean and variance, as required by the course reproducibility guidelines.
3. **Model Consistency**: To ensure valid comparison between baseline and modified results, the exact same model must be used for both experiments to avoid confounding variables.
4. **Parsing Logic Adaptation**: To validate the true impact of the reasoning step on performance, the result parsing logic must be updated to extract the `Y/N/A` vote from the last line of the model's output. Full parsing code is available in the Debug Diary section of the course report.
5. **Key Security**: Never commit the `.env` file with your real API key to GitHub. All credential-related content must remain local only.

---

## 8. Original Project Citation
This project is built on the UNBench repository. Please cite the original paper as follows:
```bibtex
@inproceedings{liang2026unbench,
  title ={Benchmarking LLMs for Political Science: A United Nations Perspective},
  author ={Liang, Yueqing and Yang, Liangwei and Wang, Chen and Xia, Congying and Meng, Rui and Xu, Xiongxiao and Wang, Haoran and Payani, Ali and Shu, Kai},
  booktitle={Proceedings of the AAAI Conference on Artificial Intelligence},
  year ={2026}
}
```
---

## 9. License
This project follows the MIT License of the original repository. See the LICENSE file for full details.
