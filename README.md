# Wireshark Investigation Labs 🔍

> Learning Wireshark through real-world investigations instead of memorizing the interface.

## About

This repository documents my journey of learning **network packet analysis** using Wireshark.

Instead of following traditional GUI-focused tutorials, these labs are based on real-world scenarios where I investigate captured network traffic to understand what actually happens on the network.

The focus is on developing an analyst's mindset by asking questions such as:

- What happens when a website is opened?
- How does DNS resolution occur?
- How is a TCP connection established?
- Why are certain packets exchanged?
- How can network problems be identified from packet captures?

Each lab contains:

- Objective
- Packet Capture (.pcapng)
- Investigation Questions
- Answers
- Key observations and lessons learned

---

## Repository Structure

```text
Wireshark-Investigation-Labs/
│
├── README.md
│
├── Lab-01/
│   ├── Lab1.pcapng
│   ├── Investigation.md
│   └── screenshots/
│
├── Lab-02/
│
├── Lab-03/
│
└── ...
```

---

## Investigation Roadmap

| Lab    | Investigation                              | Status |
|--------|--------------------------------------------|--------|
| Lab 01 | Website Connection Investigation           | ✅ Completed |
| Lab 02 | Follow a TCP Conversation                  | ✅ Completed |
| Lab 03 | HTTP vs HTTPS Investigation                | ✅ Completed |
| Lab 04 | SSH Traffic Investigation                  | ✅ Completed |
| Lab 05 | ICMP & Ping Analysis                       | ✅ Completed |
| Lab 06 | DNS Resolution Deep Dive                   | ✅ Completed |
| Lab 07 | TCP Retransmissions & Connection Issues    | 🟡 Planned |
| Lab 08 | QUIC vs TCP Investigation                  | 🟡 Planned |
| Lab 09 | Network Performance Troubleshooting        | 🟡 Planned |
| Lab 10 | Mystery PCAP – Final Investigation         | 🟡 Planned |

---

## Planned Labs

- Website Connection Investigation
- Following a TCP Stream
- HTTP vs HTTPS
- SSH Packet Investigation
- ICMP & Ping Analysis
- DNS Deep Dive
- TCP Retransmissions
- QUIC Analysis
- Performance Troubleshooting
- Full Packet Investigation Challenge

---

## Goal

By the end of this repository, I aim to be able to analyze packet captures, troubleshoot common networking problems, and develop a practical understanding of network protocols through hands-on investigations.
