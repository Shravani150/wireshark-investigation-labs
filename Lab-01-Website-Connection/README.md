# Lab 01 - Website Connection Investigation

## Objective

Investigate the network communication generated when executing:

```bash
curl https://example.com
```

using Wireshark.

---

# Investigation

## Question 1

How many packets were captured?

### Answer

```
306 packets
```

---

## Question 2

Which protocols appeared in the capture?

### Answer

```
ARP
ICMPv6
DNS
QUIC
TCP
TLSv1.3
TLSv1.2
HTTP
OCSP
PKIX-CRL
```

---

## Question 3

Apply the display filter:

```
dns
```

### 3.1 How many DNS packets remain?

**Answer**

```
22 packets
```

---

### 3.2 Which packets are DNS Queries?

**Answer**

```
223
224
```

---

### 3.3 Which packets are DNS Responses?

**Answer**

```
225
226
```

---

### 3.4 Which IP addresses were returned?

**Answer**

IPv4

```
104.20.23.154
172.66.147.243
```

IPv6

```
2606:4700:90d5:72db:f2a7:8af:ef6b:ff98
```

---

## Lesson

Modern operating systems usually perform two DNS lookups:

- A Record (IPv4)
- AAAA Record (IPv6)

This explains why two DNS queries and two DNS responses were observed.

---

## Question

What would happen if DNS returned **NXDOMAIN**?

### Answer

```
The requested domain does not exist.

DNS fails
↓
No IP Address
↓
No TCP Handshake
↓
No TLS
↓
No HTTP
```

---

# TCP Investigation

Apply display filter:

```
tcp
```

---

## Question

How many TCP packets remain?

### Answer

```
82
```

---

## Observation

TLS packets were still visible even after applying the TCP filter.

### Lesson

TLS runs on top of TCP.

```
Ethernet
└── IP
      └── TCP
            └── TLS
```

Filtering for TCP displays packets that contain a TCP layer, including TLS traffic encapsulated within it.

---

## Question

Locate the first SYN packet.

### Answer

Packet Number

```
17
```

Source Port

```
51947
```

Destination Port

```
443 (HTTPS)
```

---

## Question

Locate the SYN-ACK packet.

### Answer

```
Packet 26
```

---

## Question

Locate the ACK packet.

### Answer

```
Packet 28
```

---

## Question

What is the TCP Sequence Number?

### Answer

```
1
```

---

## Lesson

Wireshark displays Relative Sequence Numbers by default.

Instead of showing the actual 32-bit sequence number, it starts the first packet at 1 to simplify analysis.

---

## Question

What is the TCP Window Size?

### Answer

```
1024

Calculated Window Size

262144
```

---

## Lesson

The TCP Window Size represents how much data the receiver is currently willing to accept before sending an acknowledgement.

This mechanism provides flow control and improves transmission efficiency.

---

## Question

Why is the TCP Segment Length 0?

### Answer

```
The SYN packet contains no application payload.

Its purpose is only to establish the TCP connection.

Therefore, the TCP Segment Length is zero.
```

---

## Question

Why was the source port 51947 instead of 443?

### Answer

```
51947 is an ephemeral (dynamic) port selected automatically by the client operating system.

Port 443 is the server's well-known HTTPS port.
```

---

# TLS Investigation

Apply display filter:

```
tls
```

---

## Observation

First TLS packet observed:

```
TLSv1.3

Client Hello

SNI = substrate.office.com
```

---

## Lesson

Even though the experiment only involved:

```bash
curl https://example.com
```

Windows background services continued communicating over the network.

A packet capture contains traffic from all active applications.

A network analyst must identify and isolate the relevant conversation instead of assuming every observed packet belongs to the experiment.

---

# Lab Summary

### Concepts Practiced

- DNS Resolution
- TCP Three-Way Handshake
- Display Filters
- Relative Sequence Numbers
- TCP Segment Length
- TCP Window Size
- TLS Encapsulation
- Ephemeral Ports
- Background Network Traffic

---

## Key Takeaway

Packet analysis is not about looking at every packet.

It is about identifying the conversation that belongs to the investigation and reasoning about each step in that communication.
