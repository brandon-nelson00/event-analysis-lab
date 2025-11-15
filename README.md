# Event Analysis Lab (Network Forensics Investigation)

This repository contains a detailed lab report for **Event Analysis Lab**, undertaken as part of the course *IS-3523 Intrustion Detection and Response*.  The objective was to investigate network events captured in a PCAP from a home network, quantify traffic statistics, map hosts and services, evaluate intrusion‑detection alerts, and identify any anomalies or misconfigurations.

## Project overview

During this lab I:

* Loaded the capture into **Wireshark** and measured the session duration (~8 minutes 25 seconds), packet count (2 449), and overall throughput.
* Used Wireshark’s **Protocol Hierarchy** and **I/O Graph** to quantify protocol distributions (TCP, UDP, HTTP, TLS, FTP) and visualise traffic spikes caused by web browsing.
* Examined **DNS** requests to AOL (`dial.internet.aol.com`) and 2wire.net domains to understand the ISP environment and browsing activity.
* Leveraged **NetworkMiner** to identify devices on the LAN (workstation `KAUFMANUPSTAIRS`, DVR, IP camera, gateway) and analysed the ARP cache to map MAC/IP associations.
* Ran **Snort** in offline mode to generate alerts such as *HTTP server response before client request*, *reset outside window*, *consecutive small segments* and *TCP portsweep*, then correlated them to benign causes like caching, retransmissions and service discovery.
* Investigated a suspected ARP poisoning alert and confirmed it was a false positive caused by a device reconnecting with a new MAC address.
* Identified security weaknesses including unencrypted protocols (HTTP/FTP), anonymous FTP access and outdated infrastructure.

The complete lab write‑up, including figures extracted from the original lab document, can be found in [`lab_report.md`](lab_report.md).

## Repository structure

```
event-analysis-lab/
├── img-000.jpg    # Wireshark PCAP summary (capture statistics)
├── img-001.jpg    # Protocol hierarchy statistics
├── img-002.jpg    # I/O graph showing throughput spike
├── img-003.jpg    # DNS query details for AOL/2wire
├── img-004.jpg    # NetworkMiner host table
├── img-005.jpg    # ARP table (MAC/IP associations)
├── img-006.jpg    # Snort processing PCAP in IDS mode
├── img-007.jpg    # Snort alerts output
├── img-008.jpg    # IP addresses flagged in Snort alerts
├── lab_report.md # Detailed procedural write‑up with embedded figures
└── README.md     # Overview and quick reference (this file)
```

## Usage

1. Clone this repository or download the ZIP.
2. Open `lab_report.md` to read the detailed network forensics analysis.  The report can be viewed directly on GitHub or rendered locally with any Markdown viewer.
3. The image files (`img-000.jpg` through `img-008.jpg`) contain screenshots extracted from the original lab PDF and are embedded in the report for context.

## Reproducibility

To reproduce the analysis outlined in this lab, you will need a packet capture from a similar environment.  The tools and commands used include:

* **Wireshark** for packet inspection, statistics and I/O graphs.
* **NetworkMiner** for passive host identification and session reconstruction.
* **Snort IDS** for offline intrusion‑detection scanning of the PCAP (`snort –r capture.pcap –c /etc/snort/snort.conf`).

Ensure you conduct this analysis in a lab environment with appropriate authorization.
