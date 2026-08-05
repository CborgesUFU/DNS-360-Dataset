# UFU-Do53-EXF and UFU-DoH-EXF

Datasets, packet captures, feature-extraction pipelines, and supervised machine-learning baselines for detecting data exfiltration over conventional DNS and DNS over HTTPS.

This repository contains the research artifacts associated with the paper:

> **UFU-Do53-EXF and UFU-DoH-EXF: Datasets for Detecting Data Exfiltration over DNS**

## Authors

- Cristiano L. M. Borges
- Rodrigo S. Miani

Faculty of Computing  
Federal University of Uberlândia — UFU  
Uberlândia, Minas Gerais, Brazil

---

## Overview

This repository provides two complementary datasets:

- **UFU-Do53-EXF**, designed for detecting data exfiltration over conventional DNS on port 53;
- **UFU-DoH-EXF**, designed for detecting data exfiltration over DNS over HTTPS on TCP port 443.

The repository includes:

- structured CSV datasets;
- raw PCAP and PCAPNG captures;
- PCAP-to-Flow feature-extraction pipelines;
- supervised machine-learning scripts;
- the Tranco domain-list snapshot used to support benign traffic generation;
- documentation required for artifact evaluation and reproducibility.

The artifact supports the following workflow:

```text
Controlled traffic generation
            |
            v
PCAP or PCAPNG capture
            |
            v
Protocol-specific feature extraction
            |
            v
Structured CSV dataset
            |
            v
Supervised machine-learning evaluation
```

---

## Artifact badges requested

The following artifact badges are requested for evaluation:

- **Artifacts Available**
- **Artifacts Functional**
- **Artifacts Sustainable**
- **Experiments Reproducible**

---

## Dataset summary

### UFU-Do53-EXF

| Property | Value |
|---|---:|
| Input features | 29 |
| Total samples | 5,351,064 |
| Benign samples | 4,812,119 |
| Exfiltration samples | 538,945 |
| Exfiltration percentage | 10.0706% |

### UFU-DoH-EXF

| Property | Value |
|---|---:|
| Input features | 28 |
| Total samples | 1,048,575 |
| Benign samples | 1,016,438 |
| Exfiltration samples | 32,137 |
| Exfiltration percentage | 3.0648% |

For the supervised UFU-DoH-EXF case study, exact duplicate rows were removed before the stratified training and testing split.

| Property after duplicate removal | Value |
|---|---:|
| Total samples | 1,022,257 |
| Benign samples | 992,111 |
| Exfiltration samples | 30,146 |

The 20% test partition contained:

| Property | Value |
|---|---:|
| Total test samples | 204,452 |
| Benign test samples | 198,423 |
| Exfiltration test samples | 6,029 |

---

## Repository structure

```text
UFU-Do53-EXF-and-UFU-DoH-EXF/
|
├── data/
│   ├── CSV/
│   │   ├── Do53/
│   │   ├── DoH/
│   │   └── README.md
│   └── PCAP/
│       ├── Do53/
│       ├── DoH/
│       └── README.md
|
├── Pipe/
│   ├── PCAP to FLOW-DO53.py
│   ├── PCAP to FLOW-DOH.py
│   ├── dns_exfil_samples/
│   ├── Traffic Generation Methodology
│   └── README.md
|
├── scripts/
│   ├── Machine Learning Models/
│   │   ├── ML_Do53_RF_V3.py
│   │   ├── ML_Do53_SVM_V1.py
│   │   ├── ML_Do53_XGBoost_V1.py
│   │   ├── ML_DoH_RF_V1.py
│   │   ├── ML_DoH_SVM_V1.py
│   │   ├── ML_DoH_XGBoost_V1.py
│   │   └── README.md
│   ├── Tranco List/
│   │   ├── top-1m.csv
│   │   └── README.md
│   └── README.md
|
├── CITATION.cff
├── License.md
├── README.md
└── Requirements.md
```

The `Pipe` directory is located at the repository root. It is not a subdirectory of `scripts`.

---

## Experimental environment

The network captures were generated in a controlled laboratory deployed in Amazon Web Services. The environment separated victim, attacker, routing, logging, and supporting roles into logically distinct components.

The laboratory included:

- an Ubuntu-based victim machine;
- a Kali Linux attacker machine;
- a Windows server for supporting functions;
- a FortiGate virtual appliance;
- a FortiAnalyzer instance;
- DNS and DoH resolvers;
- packet-capture points;
- controlled attacker infrastructure.

The malicious Do53 scenario used `dnscat2`. The malicious DoH scenario used a custom client that fragmented and transmitted data through HTTPS requests compatible with the DNS over HTTPS communication model.

---

## Hardware requirements

### Documentation and small-file inspection

- 8 virtual CPUs;
- 32 GB RAM;
- 100 GB SSD storage.

### Reduced functional execution

- 16 virtual CPUs;
- 64 GB RAM;
- 250 GB high-performance SSD storage.

### Complete machine-learning experiments

- 32 virtual CPUs;
- 128 GB RAM;
- 500 GB to 1 TB high-performance SSD or NVMe storage;
- at least 200 GB of free temporary working space.

### Complete PCAP-to-Flow regeneration

- 48 to 64 virtual CPUs;
- 192 GB to 256 GB RAM;
- at least 1 TB high-performance SSD or NVMe storage;
- additional free storage for captures, intermediate structures, and final CSV files.

A GPU is not required. CPU capacity, system memory, and storage throughput are the primary resource constraints.

---

## Software requirements

Recommended environment:

```text
Python 3.11
Git
pip
Python virtual environment support
```

Main Python packages:

```text
dpkt
numpy
pandas
scikit-learn
xgboost
matplotlib
shap
openpyxl
psutil
tqdm
joblib
```

Install the versions specified in `Requirements.md`.

---

## Installation

Clone the repository:

```bash
git clone https://github.com/CborgesUFU/UFU-Do53-EXF-and-UFU-DoH-EXF.git
cd UFU-Do53-EXF-and-UFU-DoH-EXF
```

Create a virtual environment:

```bash
python3.11 -m venv .venv
```

Activate it on Linux or macOS:

```bash
source .venv/bin/activate
```

Activate it on Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Install the dependencies listed in `Requirements.md`.

---

## Dataset labels

The expected labels are:

```text
Benign
Exfiltration
```

The semantic label is determined by the controlled experimental scenario. In the current PCAP-to-Flow implementation, this scenario label is operationally recovered from the source capture filename according to the documented naming convention.

The relationship between the experimental scenario, capture filename, generated structured record, and assigned label must remain traceable throughout the pipeline.

---

## PCAP-to-Flow pipelines

The feature-extraction scripts are located in the root-level `Pipe` directory.

### UFU-Do53-EXF

```bash
python "Pipe/PCAP to FLOW-DO53.py"
```

Expected input:

```text
data/PCAP/Do53
```

Recommended generated-output location:

```text
results/Pipe/Do53
```

### UFU-DoH-EXF

```bash
python "Pipe/PCAP to FLOW-DOH.py"
```

Expected input:

```text
data/PCAP/DoH
```

Recommended generated-output location:

```text
results/Pipe/DoH
```

The UFU-DoH-EXF pipeline produces the 28 flow-based features described in the paper. The public documentation and paper-reproduction procedure use only these 28 features.

---

## Machine-learning experiments

The six scripts are located under:

```text
scripts/Machine Learning Models
```

### Do53 experiments

The Do53 scripts use a predefined 80/20 partition stored in separate training and testing directories. The split is created before script execution.

```bash
python "scripts/Machine Learning Models/ML_Do53_RF_V3.py"
python "scripts/Machine Learning Models/ML_Do53_XGBoost_V1.py"
python "scripts/Machine Learning Models/ML_Do53_SVM_V1.py"
```

### DoH experiments

The DoH scripts load the dataset, remove exact duplicate rows, and create an internal stratified 80/20 split with `random_state=42`.

```bash
python "scripts/Machine Learning Models/ML_DoH_RF_V1.py"
python "scripts/Machine Learning Models/ML_DoH_XGBoost_V1.py"
python "scripts/Machine Learning Models/ML_DoH_SVM_V1.py"
```

Recommended output locations:

```text
results/Machine Learning/Do53
results/Machine Learning/DoH
```

---

## Reference model configurations

### UFU-Do53-EXF Random Forest

```text
Model: Random Forest
Number of trees: 100
Model random state: 50
Class weight: balanced
Training and testing strategy: predefined 80/20 partition
Split generation: performed before script execution
```

### UFU-Do53-EXF XGBoost

```text
Number of estimators: 300
Maximum depth: 6
Learning rate: 0.1
Subsample: 0.8
Column sample by tree: 0.8
Model random state: 50
Training and testing strategy: predefined 80/20 partition
Split generation: performed before script execution
```

### UFU-Do53-EXF SVM

```text
Kernel: radial basis function
C: 1.0
Gamma: scale
Class weight: balanced
Numerical scaling: StandardScaler
Training and testing strategy: predefined 80/20 partition
Split generation: performed before script execution
```

### UFU-DoH-EXF Random Forest

```text
Number of trees: 100
Random state: 42
Class weight: balanced
Exact duplicate removal: enabled
Training partition: 80%
Testing partition: 20%
Stratified split: enabled
```

### UFU-DoH-EXF XGBoost

```text
Number of estimators: 300
Maximum depth: 6
Learning rate: 0.1
Subsample: 0.8
Column sample by tree: 0.8
Objective: binary:logistic
Evaluation metric: logloss
Random state: 42
Exact duplicate removal: enabled
Training partition: 80%
Testing partition: 20%
Stratified split: enabled
```

### UFU-DoH-EXF Linear SVM

```text
C: 1.0
Maximum iterations: 10,000
Class weight: balanced
Random state: 42
Numerical scaling: StandardScaler
Exact duplicate removal: enabled
Training partition: 80%
Testing partition: 20%
Stratified split: enabled
```

---

## Expected results

### UFU-Do53-EXF

| Model | Accuracy | Balanced Accuracy | Macro Precision | Macro Recall | Macro F1 | Weighted F1 | FP | FN |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Random Forest | 0.999996 | 0.999981 | 0.999998 | 0.999981 | 0.999990 | 0.999996 | 0 | 4 |
| XGBoost | 0.999998 | 0.999991 | 0.999999 | 0.999991 | 0.999995 | 0.999998 | 0 | 2 |
| SVM | 0.999915 | 0.999817 | 0.999714 | 0.999817 | 0.999765 | 0.999915 | 58 | 33 |

### UFU-DoH-EXF

| Model | Accuracy | Balanced Accuracy | Macro Precision | Macro Recall | Macro F1 | Weighted F1 | FP | FN |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Random Forest | 0.999985 | 0.999832 | 0.999912 | 0.999832 | 0.999872 | 0.999985 | 1 | 2 |
| XGBoost | 0.999985 | 0.999832 | 0.999912 | 0.999832 | 0.999872 | 0.999985 | 1 | 2 |
| Linear SVM | 0.999863 | 0.999688 | 0.997927 | 0.999688 | 0.998806 | 0.999863 | 25 | 3 |

Small numerical variations may occur because of Python and library versions, operating system, CPU architecture, file order, parallel execution, and floating-point implementation.

---

## Security considerations

This repository contains controlled representations of data-exfiltration behavior.

Use the artifacts only:

- for scientific research;
- for cybersecurity education;
- for authorized defensive evaluation;
- in isolated laboratory environments;
- on systems and networks owned by the evaluator or explicitly authorized.

Do not execute traffic-generation components against production or third-party infrastructure. Do not use real confidential, personal, or corporate data.

Offline CSV evaluation and PCAP processing do not require generating new live exfiltration traffic.

---

## Reproducibility notes

To reproduce the reported experiments as closely as possible, preserve:

- the original CSV and PCAP files;
- the capture filenames;
- the published feature columns;
- the predefined Do53 training and testing partition;
- the internal DoH duplicate-removal and stratified split procedure;
- the model parameters;
- the random seeds;
- the dependency versions;
- the file-loading order;
- the output logs and metrics.

Generated files should be stored under:

```text
results/Pipe/Do53
results/Pipe/DoH
results/Machine Learning/Do53
results/Machine Learning/DoH
```

---

## Limitations

The datasets were generated in a controlled laboratory and do not represent every condition found in production networks.

The reported experiments are intra-dataset evaluations. They demonstrate internal consistency and discriminative capability under the evaluated scenarios, but they do not independently demonstrate generalization to unseen networks, resolvers, capture periods, or exfiltration implementations.

---

## Citation

```bibtex
@inproceedings{borges2026ufudnsexf,
  title     = {UFU-Do53-EXF and UFU-DoH-EXF: Datasets for Detecting Data Exfiltration over DNS},
  author    = {Borges, Cristiano L. M. and Miani, Rodrigo S.},
  year      = {2026}
}
```

Complete publication metadata should be added after publication.

---

## License

The source code, datasets, packet captures, and documentation are distributed according to the terms described in `License.md`.
