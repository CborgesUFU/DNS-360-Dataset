# Structured CSV datasets

This directory contains the structured CSV representations of the UFU-Do53-EXF and UFU-DoH-EXF datasets.

## Directory structure

```text
CSV/
├── Do53/
├── DoH/
└── README.md
```

## UFU-Do53-EXF

Directory:

```text
data/CSV/Do53
```

| Property | Value |
|---|---:|
| Input features | 29 |
| Total samples | 5,351,064 |
| Benign samples | 4,812,119 |
| Exfiltration samples | 538,945 |
| Exfiltration percentage | 10.0706% |

## UFU-DoH-EXF

Directory:

```text
data/CSV/DoH
```

| Property | Value |
|---|---:|
| Input features | 28 |
| Total samples | 1,048,575 |
| Benign samples | 1,016,438 |
| Exfiltration samples | 32,137 |
| Exfiltration percentage | 3.0648% |

For the supervised case study, exact duplicate rows were removed before the internal stratified 80/20 split.

| Property after duplicate removal | Value |
|---|---:|
| Total samples | 1,022,257 |
| Benign samples | 992,111 |
| Exfiltration samples | 30,146 |

## Labels

```text
Benign
Exfiltration
```

The semantic label is defined by the controlled experimental scenario. The processing pipeline recovers that label from the capture filename according to the documented naming convention.

## Data integrity

Do not modify or overwrite the original CSV files.

Store normalized files, sampled files, training and testing partitions, predictions, plots, logs, and evaluation results outside this directory.

Recommended output locations:

```text
results/Machine Learning/Do53
results/Machine Learning/DoH
```

## Loading multiple CSV files

```python
from pathlib import Path
import pandas as pd

csv_directory = Path("data/CSV/Do53")
csv_files = sorted(csv_directory.glob("*.csv"))

if not csv_files:
    raise FileNotFoundError(f"No CSV files were found in {csv_directory}")

dataframes = [
    pd.read_csv(csv_file, low_memory=False)
    for csv_file in csv_files
]

dataset = pd.concat(dataframes, ignore_index=True)

print(f"Files loaded: {len(csv_files):,}")
print(f"Rows loaded: {len(dataset):,}")
print(f"Columns loaded: {len(dataset.columns):,}")
```

Sorting filenames before loading supports deterministic processing order.

## Resource requirements

Recommended for complete loading and preprocessing:

- 32 virtual CPUs;
- 128 GB RAM;
- 500 GB to 1 TB high-performance SSD or NVMe storage.

For repeated preprocessing or simultaneous experiments, 192 GB to 256 GB RAM may be appropriate.
