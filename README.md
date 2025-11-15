# Event Analysis Lab

## Overview / Objective

This lab focuses on network forensics and intrusion‑detection techniques.  The goal is to analyze a captured packet capture (PCAP) from a home network to identify unusual events, understand traffic patterns and detect evidence of malicious activity.  By processing the trace through Wireshark, NetworkMiner and Snort, the lab builds hands‑on skills in protocol analysis, host enumeration, timeline reconstruction and intrusion detection.  In particular, the assignment demonstrates how to quantify traffic statistics, map internal devices, identify application protocols and inspect intrusion‑detection system (IDS) alerts【320472619365317†L16-L24】.  Ultimately, the objective is to differentiate benign activity from potential threats and recommend controls to harden the network.

## Environment Setup

The analysis was performed in a controlled lab environment with the following configuration:

| Component | Description |
|---|---|
| **Capture file** | Provided PCAP representing approximately 8 minutes and 25 seconds of traffic captured on 30 October 2005【320472619365317†L28-L33】.  The trace contains 2 449 packets totaling about 811 KB, reflecting typical home network activity. |
| **Analysis workstation** | Kali Linux virtual machine (VM) equipped with Wireshark 4.x, NetworkMiner 2.x and Snort 2.x.  Tools were installed using standard package managers. |
| **Network target** | A home LAN using the private 172.16.0.0/16 subnet.  The primary host (KAUFMANUPSTAIRS) uses IP 172.16.1.35, with other devices such as a DVR at 172.16.1.37 and an IP camera at 172.16.1.39【320472619365317†L125-L136】.  The default gateway is 172.16.0.1. |
| **Intrusion‑detection system** | Snort configured in offline analysis mode to process the PCAP using the default ruleset.  Alerts were generated without active blocking to study detection logic. |

## Methodology / Steps Performed

1. **Initial traffic inspection with Wireshark** – Loaded the PCAP in Wireshark and reviewed the overall capture statistics.  Noted session duration, packet count and total bytes【320472619365317†L28-L33】.  Utilized the *Statistics → Protocol Hierarchy* and *IO Graph* features to examine protocol distribution and visualize traffic spikes【320472619365317†L38-L49】.
2. **Protocol analysis** – Identified the most common protocols: TCP (≈84.9 %), UDP (≈10 %), HTTP (7.3 %), FTP (8.1 %) and TLS (3.6 %)【320472619365317†L38-L49】.  Inspected individual streams to understand web browsing, file transfers and encrypted sessions.  Observed high volumes of TCP acknowledgments and retransmissions leading to a burst of traffic around 17:31:09【320472619365317†L54-L80】.
3. **Host and network mapping** – Used Wireshark and NetworkMiner to identify hostnames, IP addresses and MAC addresses.  Determined that the main workstation is named **KAUFMANUPSTAIRS** with IP 172.16.1.35【320472619365317†L103-L116】 and runs Windows【320472619365317†L117-L122】.  Documented other LAN devices (router, DVR and IP camera) and unused addresses noted in ARP traffic【320472619365317†L125-L139】【320472619365317†L169-L176】.
4. **Service and application inspection** – Reviewed DNS queries and HTTP requests to determine external services accessed.  Observed connections to AOL dial‑up services (dial.internet.aol.com) and 2Wire.net, indicating the ISP and modem infrastructure【320472619365317†L83-L99】.  Examined FTP sessions to identify anonymous logins and file transfers.
5. **Anomaly investigation** – Noticed a spike in traffic due to a burst of HTTP requests coupled with TLS handshakes and retransmissions【320472619365317†L54-L80】.  Investigated ARP traffic flagged by NetworkMiner as possible ARP poisoning; correlated MAC addresses and confirmed there were no duplicate ARP replies and the anomaly resulted from legitimate device behaviour【320472619365317†L177-L215】.  Explored DNS and HTTP patterns to check for reconnaissance or exfiltration activities.
6. **IDS processing with Snort** – Ran Snort in packet‑inspection mode against the PCAP.  Captured alerts and categorized them by signature.  Key alerts included *PROTOCOL‑OTHER HTTP server response before client request* (261 occurrences), *Reset outside window* (109), *Consecutive TCP small segments exceeding threshold* (14) and *TCP Portsweep* (3), indicating potential TCP session manipulation and reconnaissance【320472619365317†L239-L255】.
7. **Validation and reporting** – Correlated Snort alerts with packet context to determine whether they represented malicious activity or false positives.  Verified that no active exploits were found, but outdated practices such as unencrypted HTTP sessions and anonymous FTP access were noted【320472619365317†L219-L233】.  Prepared this report summarizing findings and recommending mitigations.

## Key Findings / Results

### Traffic statistics

* **Capture duration:** Approximately 8 min 25 s (17:29:35–17:38:00 on 30 October 2005) with 2 449 packets and 811 157 bytes captured【320472619365317†L28-L33】.
* **Protocol distribution:** TCP dominated the trace at **84.9 %**, followed by UDP (**10 %**), HTTP (**7.3 %**), FTP (**8.1 %**) and TLS (**3.6 %**)【320472619365317†L38-L49】.  Minor protocols included NetBIOS/SMB, ARP and DNS【320472619365317†L48-L53】.
* **Traffic spike:** At 17:31:09, the host generated a flurry of HTTP GET requests for web content alongside TLS negotiations, retransmissions and duplicate ACKs, causing a rapid increase in throughput.  Out‑of‑order packets and TCP resets suggested network congestion or the closing of a browser session【320472619365317†L54-L80】.

### Host and network identification

* **Primary host:** KAUFMANUPSTAIRS (Windows), IP 172.16.1.35【320472619365317†L103-L122】.  This machine initiated most network traffic and performed DNS queries to AOL’s ISP infrastructure【320472619365317†L83-L99】.
* **Network topology:** A router at 172.16.0.1 acts as the gateway, connecting multiple devices on the 172.16.x.x subnet.  Additional devices include a DVR (TiVo) at 172.16.1.37 and an IP camera at 172.16.1.39【320472619365317†L125-L136】.  Two additional addresses (172.16.1.34 and 172.16.1.36) appeared only in ARP requests, indicating dormant or offline devices【320472619365317†L169-L176】.
* **ISP access:** The capture shows connections to dial.internet.aol.com and 2Wire.net, implying that the home network used AOL dial‑up/DSL services【320472619365317†L83-L99】.  No evidence of visits to an ISP customer portal was found.
* **No lateral movement:** There was no evidence of the host communicating with other LAN computers; traffic was directed mainly to the gateway and external hosts【320472619365317†L155-L163】.

### Anomaly analysis

* **ARP poisoning suspicion:** NetworkMiner reported that IP 172.16.1.39 had multiple MAC addresses, raising suspicion of ARP spoofing.  Upon deeper inspection, there were no duplicate ARP replies; the MAC change likely resulted from a device reconnecting or switching interfaces【320472619365317†L177-L205】.  Thus, ARP poisoning was deemed a false positive【320472619365317†L207-L215】.
* **Snort alerts:** Snort produced several alerts indicating potential TCP manipulation and reconnaissance.  The most common was **“HTTP server response before client request”** (261 events).  “Reset outside window” (109) suggested irregular TCP session termination, while “Consecutive TCP small segments exceeding threshold” (14) may signal fragmentation attempts.  Only three alerts flagged as **“TCP Portsweep”**, indicating limited scanning activity【320472619365317†L239-L255】.  After correlation, these were attributed to benign network behaviour or poor protocol implementations.

### Security observations

* **Unencrypted protocols:** The presence of HTTP and anonymous FTP traffic exposes credentials and content to interception【320472619365317†L219-L233】.
* **Outdated infrastructure:** Using dial‑up/DSL services and legacy protocols (NetBIOS/SMB) indicates the environment lacks modern protections such as TLS‑only services or firewalling.
* **Recommendations:** Enforce HTTPS for web browsing, disable anonymous FTP, monitor for port scanning, and deploy updated IDS signatures.  Regularly review ARP tables to detect spoofing, and ensure devices receive firmware updates.

## Screenshots Section

Include screenshots in your repository to enhance the report.  Save images in a `/images` folder and reference them below.

```md
![PCAP details and statistics](images/pcap_statistics.png)
![Protocol breakdown and I/O graph](images/protocol_breakdown.png)
![Wireshark I/O graph – traffic spike](images/io_graph.png)
![DNS queries for AOL – network analysis](images/dns_queries.png)
![Local area network device overview](images/lan_devices.png)
![ARP broadcast traffic analysis](images/arp_traffic.png)
![Snort processing PCAP in IDS mode](images/snort_processing.png)
![Snort alerts showing detected anomalies](images/snort_alerts.png)
![IP addresses flagged in Snort alerts](images/snort_ips.png)
```

## Technologies & Tools Used

* **Wireshark** – GUI protocol analyser used to inspect PCAP files, generate statistics and graph traffic patterns【320472619365317†L16-L24】.
* **NetworkMiner** – Passive network‑forensics tool used to reconstruct hosts, sessions and files and to detect anomalies like ARP poisoning【320472619365317†L177-L205】.
* **Snort** – Open‑source intrusion‑detection system configured in offline mode to process the capture and generate alerts based on signature rules【320472619365317†L239-L255】.
* **Kali Linux** – Penetration testing distribution hosting the analysis tools, providing additional utilities such as Nmap and Metasploit if needed.
* **PCAP data** – Captured network traffic representing a home LAN over dial‑up/DSL connectivity.

## Skills Demonstrated

* **Network protocol analysis:** Parsed packet capture to understand TCP/UDP flows, application protocols and traffic spikes using Wireshark’s statistical tools【320472619365317†L28-L33】【320472619365317†L38-L49】.
* **Host enumeration:** Identified hosts, IP addresses and operating systems through ARP and DNS analysis and cross‑referenced with NetworkMiner’s host information【320472619365317†L103-L122】【320472619365317†L125-L136】.
* **Threat detection:** Interpreted Snort alerts and distinguished between benign and malicious events; investigated ARP poisoning suspicion and validated it as a false positive【320472619365317†L177-L215】【320472619365317†L239-L255】.
* **Log correlation:** Correlated IDS alerts with packet data to validate triggers and rule out false positives, simulating a SOC analyst’s workflow.
* **Security assessment:** Identified insecure protocols and recommended mitigation strategies such as encryption enforcement and removal of anonymous FTP【320472619365317†L219-L233】.

## Conclusion

This lab provided a comprehensive exercise in network forensics and intrusion detection.  By systematically analyzing a packet capture with Wireshark, NetworkMiner and Snort, I learned to quantify traffic statistics, identify hosts and services, interpret IDS alerts and investigate anomalies.  The capture depicted normal home‑network behaviour mixed with outdated protocols and a few false positives, illustrating the importance of context when evaluating alerts.  Key takeaways include the need for encrypted protocols (HTTPS, SFTP), proper network segmentation and continuous monitoring.  These skills align closely with SOC analyst responsibilities, demonstrating competency in log analysis, threat detection, enumeration and protocol analysis.  The experience strengthens my ability to triage network events, differentiate legitimate traffic from threats and recommend security improvements for real‑world environments【320472619365317†L258-L274】.
