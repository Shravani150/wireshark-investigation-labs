# Case 04 – SSH Traffic Investigation

## Objective

Investigate an SSH login session and determine what information is visible in a packet capture before and after SSH encryption is established.

---

## Evidence

* `Case-04-ssh-bandit-login.pcapng`

Traffic generated using:

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

---

# Step 1 – Isolate SSH Traffic

Display filter:

```text
ssh || tcp.port == 2220
```

### Findings

| Item              | Value          |
| ----------------- | -------------- |
| Packets displayed | `149`          |
| Server IP         | `51.20.162.29` |
| Source port       | `54981`        |
| Destination port  | `2220`         |

---

# Step 2 – TCP Handshake

| Handshake Packet | Packet No. |
| ---------------- | ---------- |
| SYN              | `265`      |
| SYN, ACK         | `266`      |
| ACK              | `267`      |

The TCP connection was established successfully before any SSH encryption began.

---

# Step 3 – SSH Version Negotiation

### Client Banner

```text
SSH-2.0-OpenSSH_for_Windows_9.5
```

### Server Banner

```text
SSH-2.0-OpenSSH_10.2p1
```

### Protocol Version

`SSHv2`

These banners were visible in plaintext because SSH version negotiation occurs before encryption is enabled.

---

# Step 4 – Authentication Visibility

### Username Search

Display filter:

```text
frame contains "bandit0"
```

**Result:** Not found.

### Password Search

Display filter:

```text
frame contains "<bandit_password>"
```

**Result:** Not found.

### Observation

Neither the username nor the password was visible in the packet capture.

---

# Step 5 – Follow TCP Stream

The TCP stream appeared encrypted and did not reveal shell commands such as `ls`, `cat`, or `exit`.

---

# Important Observation – SSH vs TLS

A key discovery in this investigation was that **SSH does not use TLS/SSL**.

### HTTPS

```text
TCP
└── TLS
    └── HTTP
```

### SSH

```text
TCP
└── SSH
    └── Shell Session
```

Both protocols provide encryption, but they use different application-layer protocols.

---

# SSH Connection Flow

```text
TCP Handshake
      ↓
SSH Version Exchange
      ↓
SSH Key Exchange
      ↓
Host Key Verification
      ↓
Session Keys Created
      ↓
Encrypted Authentication
      ↓
Encrypted Commands & Output
```

Encryption begins after the SSH key exchange completes.

---

# Investigation Conclusion

Wireshark could see the TCP handshake and SSH version banners because those packets were exchanged before encryption was established. After the SSH key exchange, the client and server created shared session keys, and all authentication data, commands, and terminal output became encrypted. Therefore the password and commands were not readable in the packet capture.

---

# Key Learnings

* TCP setup is visible even for secure protocols.
* SSH exposes a version banner before encryption.
* SSH does **not** use TLS.
* Encryption begins after the SSH key exchange.
* Passwords are not visible in SSH traffic.
* Commands and terminal output are encrypted.
