# Packet captures

This directory contains the PCAP or PCAPNG captures used to construct UFU-Do53-EXF and UFU-DoH-EXF.

## Directory structure

```text
PCAP/
├── Do53/
├── DoH/
└── README.md
```

## UFU-Do53-EXF captures

Directory:

```text
data/PCAP/Do53
```

The captures include:

- controlled benign conventional DNS traffic;
- controlled DNS data-exfiltration traffic.

The corresponding feature-extraction script is:

```text
Pipe/PCAP to FLOW-DO53.py
```

## UFU-DoH-EXF captures

Directory:

```text
data/PCAP/DoH
```

The captures include:

- controlled benign DNS over HTTPS traffic;
- controlled DoH data-exfiltration traffic.

The corresponding feature-extraction script is:

```text
Pipe/PCAP to FLOW-DOH.py
```

## Labeling procedure

The semantic label of each generated sample is defined by the experimental scenario associated with its source capture. The current processing scripts recover this label from the capture filename, which must follow the documented naming convention.

Recommended filename examples:

```text
Do53_Benign_001.pcap
Do53_Exfiltration_001.pcap
DoH_Benign_001.pcap
DoH_Exfiltration_001.pcap
```

## Data integrity

Do not modify, rename, or overwrite the original captures after publication. Capture filenames are part of the traceability mechanism used by the current pipeline.

## Security warning

These captures contain controlled representations of data-exfiltration behavior.

Do not:

- replay them on production networks;
- run related traffic-generation tools against third-party infrastructure;
- use real or sensitive data;
- disable production security controls to access the files.

Some security tools may classify the captures as suspicious or malicious. Use an isolated research environment.

## Processing requirements

Recommended for complete processing:

- 48 to 64 virtual CPUs;
- 192 GB to 256 GB RAM;
- at least 1 TB high-performance SSD or NVMe storage.
