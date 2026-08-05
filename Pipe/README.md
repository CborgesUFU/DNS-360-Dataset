# PCAP-to-Flow feature-extraction pipelines

This root-level directory contains the scripts and supporting materials used to transform raw PCAP or PCAPNG captures into the structured CSV datasets associated with UFU-Do53-EXF and UFU-DoH-EXF.

## Directory contents

```text
Pipe/
├── PCAP to FLOW-DO53.py
├── PCAP to FLOW-DOH.py
├── dns_exfil_samples/
├── Traffic Generation Methodology
└── README.md
```

| Script | Dataset | Input | Main output |
|---|---|---|---|
| `PCAP to FLOW-DO53.py` | UFU-Do53-EXF | DNS PCAP or PCAPNG files | `dataset_Do53.csv` |
| `PCAP to FLOW-DOH.py` | UFU-DoH-EXF | TCP/443 PCAP or PCAPNG files | `dataset_DoH.csv` |

The scripts perform offline processing. They do not generate live network traffic.

---

## Requirements

Recommended Python version:

```text
Python 3.11
```

Required packages:

```text
dpkt
numpy
pandas
```

Recommended hardware for complete regeneration:

- 48 to 64 virtual CPUs;
- 192 GB to 256 GB RAM;
- at least 1 TB high-performance SSD or NVMe storage;
- additional space for captures and generated CSV files.

A GPU is not required.

---

## Input and output locations

Expected inputs:

```text
data/PCAP/Do53
data/PCAP/DoH
```

Recommended generated outputs:

```text
results/Pipe/Do53
results/Pipe/DoH
```

The original files under `data/PCAP` and `data/CSV` must not be overwritten.

---

## Capture naming convention

The semantic label is defined by the controlled experimental scenario. The current scripts recover the label from the capture filename.

Recommended names:

```text
Do53_Benign_001.pcap
Do53_Exfiltration_001.pcap
DoH_Benign_001.pcap
DoH_Exfiltration_001.pcap
```

The expected labels are:

```text
Benign
Exfiltration
```

Capture filenames must be reviewed before execution because incorrect names can produce incorrect labels.

---

## Supported capture formats

```text
.pcap
.pcapng
```

Supported link-layer representations include:

- Ethernet;
- Linux cooked capture version 1;
- Linux cooked capture version 2;
- raw IPv4;
- raw IPv6.

Malformed or unsupported packets are skipped.

---

# `PCAP to FLOW-DO53.py`

## Purpose

The script processes conventional DNS traffic over UDP/53 and TCP/53 and generates 29 feature columns plus the `label` column.

## Execution

```bash
python "Pipe/PCAP to FLOW-DO53.py"
```

## Main outputs

```text
dataset_Do53.csv
stats_Do53.csv
```

## Processing workflow

1. read PCAP or PCAPNG files;
2. decode IPv4 and IPv6 packets;
3. identify UDP or TCP traffic involving port 53;
4. parse DNS questions and available response metadata;
5. calculate lexical and structural features;
6. calculate domain-level aggregate statistics;
7. assign the scenario label;
8. export the final CSV dataset and class statistics.

## UFU-Do53-EXF features

```text
query_length
subdomain_length
num_labels
max_label_length
avg_label_length
entropy
ratio_digits
ratio_uppercase
ratio_lowercase
ratio_special_chars
rr_type
query_class
ttl
contains_base64_pattern
contains_hex_pattern
contains_dictionary_word
ratio_vowels
ratio_consonants
num_distinct_characters
mean_query_length_per_domain
std_query_length_per_domain
mean_entropy_per_domain
num_unique_ips
num_unique_asns
num_unique_ttls
mean_ttl
var_ttl
num_queries_per_domain
ratio_failed_queries
```

The final CSV contains these 29 features plus `label`.

## Base-domain grouping

The current implementation uses the final two labels of the queried name as the base domain. This simplified procedure does not use a Public Suffix List.

## Output behavior

The script sorts input filenames, processes all applicable captures, concatenates extracted records, calculates aggregate features, and writes one final dataset.

Because extracted records are retained before final aggregation, complete execution may require substantial memory.

---

# `PCAP to FLOW-DOH.py`

## Purpose

The script processes TCP/443 traffic and generates the 28 flow-based input features used by UFU-DoH-EXF, plus the `label` column.

## Execution

```bash
python "Pipe/PCAP to FLOW-DOH.py"
```

## Main outputs

```text
dataset_DoH.csv
doh_clienthello_report.csv
stats_DoH.csv
```

## Sampling modes

The script supports:

```text
flow
window
```

For reproduction of the paper dataset, use:

```python
SAMPLE_MODE = "window"
WINDOW_SECONDS = 5.0
```

Changing the sampling mode, window duration, or flow timeout changes the number and statistical properties of the generated samples.

## Direction definition

```text
src2dst: client to server, when destination port is 443
dst2src: server to client, when source port is 443
```

## Timing units

All timing-related features are expressed in milliseconds.

## UFU-DoH-EXF features

```text
src2dst_min_length
src2dst_max_length
src2dst_mean_length
src2dst_stddev_length
dst2src_min_length
dst2src_max_length
dst2src_mean_length
dst2src_stddev_length
bidirectional_max_length
bidirectional_mean_length
bidirectional_stddev_length
src2dst_min_time
src2dst_max_time
src2dst_mean_time
src2dst_stddev_time
dst2src_min_time
dst2src_max_time
dst2src_mean_time
dst2src_stddev_time
bidirectional_min_time
bidirectional_max_time
bidirectional_mean_time
bidirectional_stddev_time
num_src2dst
num_dst2src
flow_duration
byte_count
handshake_time
```

The final CSV contains these 28 features plus `label`.

## Missing values

Numerical values unavailable during extraction are filled according to the script configuration. The machine-learning scripts may also apply model-specific imputation.

## Output behavior

The script processes each capture and appends the resulting samples to the output CSV. It also creates per-capture processing statistics and class-level summaries.

---

## Minimal functional test

Use one small benign capture and one small exfiltration capture for each protocol.

### Do53 success criteria

- at least one capture is found;
- at least one DNS query is extracted;
- `dataset_Do53.csv` is created;
- the file contains 30 columns: 29 features and one label;
- `stats_Do53.csv` is created.

### DoH success criteria

- at least one capture is processed;
- TCP/443 packets are observed;
- at least one sample is finalized;
- `dataset_DoH.csv` is created;
- the file contains 29 columns: 28 features and one label;
- `doh_clienthello_report.csv` and `stats_DoH.csv` are created.

---

## Reproducibility requirements

Preserve:

- the original captures;
- the capture filenames;
- the same Python and package versions;
- the same input-file ordering;
- the same sampling mode;
- the same window duration;
- the same flow timeout;
- the same missing-value policy;
- the same feature lists;
- the same labeling convention.

---

## Security considerations

These scripts perform offline packet-capture analysis. They do not establish tunnels, transmit files, replay traffic, or contact external infrastructure.

Use them only with authorized captures in a controlled research environment.
