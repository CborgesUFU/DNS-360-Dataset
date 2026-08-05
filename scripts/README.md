# Scripts

This directory contains the supervised machine-learning experiments and the Tranco domain-list material used by the artifact.

The PCAP-to-Flow pipelines are not located here. They are located in the root-level `Pipe` directory.

## Directory structure

```text
scripts/
├── Machine Learning Models/
│   ├── ML_Do53_RF_V3.py
│   ├── ML_Do53_SVM_V1.py
│   ├── ML_Do53_XGBoost_V1.py
│   ├── ML_DoH_RF_V1.py
│   ├── ML_DoH_SVM_V1.py
│   ├── ML_DoH_XGBoost_V1.py
│   └── README.md
├── Tranco List/
│   ├── top-1m.csv
│   └── README.md
└── README.md
```

## Machine Learning Models

The six scripts evaluate:

- Random Forest;
- XGBoost;
- Support Vector Machine.

Scripts containing `Do53` use UFU-Do53-EXF. Scripts containing `DoH` use UFU-DoH-EXF.

The Do53 scripts use a predefined 80/20 training and testing partition stored in separate directories. The DoH scripts remove exact duplicates and create an internal stratified 80/20 split.

Recommended generated-output locations:

```text
results/Machine Learning/Do53
results/Machine Learning/DoH
```

## Tranco List

The `Tranco List` directory contains the Top 1 Million domain list used to support the controlled generation of benign traffic.

A domain's presence in the ranking does not guarantee that the domain is safe or benign. The list provides a documented selection source, not a security reputation assessment.

## Hardware requirements

### Complete machine-learning experiments

- 32 virtual CPUs;
- 128 GB RAM;
- 500 GB to 1 TB high-performance SSD or NVMe storage.

### High-capacity execution

- 48 to 64 virtual CPUs;
- 192 GB to 256 GB RAM;
- at least 1 TB high-performance SSD or NVMe storage.

A GPU is not required.

## Recommended execution procedure

1. clone the repository;
2. create a Python 3.11 virtual environment;
3. install the versions listed in `Requirements.md`;
4. verify the required CSV files;
5. configure the input and output paths;
6. execute the selected model script;
7. preserve the generated metrics and logs;
8. compare the outputs with the reference results in the root README.

## Security

The machine-learning scripts operate on previously generated CSV files. They do not need to generate live exfiltration traffic.
