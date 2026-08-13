# Case 06 – DNS Resolution Investigation

## Objective

Investigate how a domain name is resolved into an IP address and identify the DNS query, response, returned records, and caching information.

---

## Evidence

* `Case-06-dns-resolution-investigation.pcapng`

Traffic generated using:

```cmd
nslookup example.com
nslookup google.com
```

---

# Step 1 – Filter DNS Traffic

Display filter:

```text
dns
```

### Findings

| Item                  | Value     |
| --------------------- | --------- |
| Total DNS packets     | `18`      |
| DNS Server            | `fe80::1` |
| First Query Packet    | `263`     |
| First Response Packet | `264`     |

**Observation:** `fe80::1` is a link-local IPv6 address, indicating that the local router is acting as the DNS forwarder.

---

# Step 2 – Inspect the First DNS Query

| Field            | Value         | Explanation                                                 |
| ---------------- | ------------- | ----------------------------------------------------------- |
| Transaction ID   | `0x0002`      | Unique identifier used to match the response with the query |
| Query Name       | `example.com` | Domain name being resolved                                  |
| Query Type       | `A`           | Request for an IPv4 address                                 |
| Query / Response | `Query`       | Indicates this packet is a DNS request sent by the client   |

---

# Step 3 – Inspect the Matching DNS Response

| Item                 | Value |
| -------------------- | ----- |
| Transaction ID Match | `Yes` |
| Answer Records       | `2`   |

### Returned IPv4 Addresses

* `172.66.147.243`
* `104.20.23.154`

**Observation:** Multiple A records indicate that the domain is reachable through more than one IPv4 address.

---

# Step 4 – IPv4 and IPv6 Queries

| Query Type  | Sent | Reply Received |
| ----------- | ---- | -------------- |
| A (IPv4)    | Yes  | Yes            |
| AAAA (IPv6) | Yes  | Yes            |

* **First query sent:** `A`
* **Second query sent:** `AAAA`

---

# Step 5 – Resolution Chain

## A Record

```text
Client → DNS Server : Query example.com (A)
DNS Server → Client : example.com = 172.66.147.243
DNS Server → Client : example.com = 104.20.23.154
```

## AAAA Record

```text
Client → DNS Server : Query example.com (AAAA)
DNS Server → Client : example.com = 2606:4700:9765:72db:f2a5:8b0:ef6b:ff98
```

---

# Step 6 – NXDOMAIN Scenario

If the DNS response contained:

```text
Response Code: NXDOMAIN
```

it would mean that the domain name does not exist.

### Expected User Result

The browser would fail before any TCP or HTTPS connection begins and display an error such as:

* *Server IP address could not be found*
* *DNS_PROBE_FINISHED_NXDOMAIN*

---

# Step 7 – DNS TTL

The **Time To Live (TTL)** value is found by expanding the **Answers** section in the DNS response.

Example:

```text
example.com: type A
Time to live: 300 seconds
```

### What TTL Controls

TTL specifies **how long the DNS answer may remain in cache** before the client must perform another DNS lookup. It does **not** represent network latency or response timeout.

---

# Step 8 – Analyst Conclusion

When a user opens `https://example.com`, the client first queries its configured DNS server for the IP address of `example.com`. The DNS server checks its cache and, if necessary, performs recursive lookups through other DNS servers until it obtains the correct record. The IP address is returned to the client, which can then begin communication by opening a TCP connection to that address. The result is cached for the duration of its TTL so future requests may be answered without repeating the full DNS lookup.

---

# Key Learnings

* DNS is usually the first network conversation before web traffic.
* Queries and responses are matched using the **Transaction ID**.
* Modern systems often request both **A** and **AAAA** records.
* DNS failure prevents TCP, TLS, and HTTP from starting.
* TTL controls DNS caching duration.
* DNS typically uses **UDP**, while web connections later use **TCP**.
