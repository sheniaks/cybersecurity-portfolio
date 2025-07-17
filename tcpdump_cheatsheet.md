# 📡 tcpdump Cheat Sheet for Security+ (SY0-701)

_By Oleksandr Sheniak — GitHub: [sheniaks](https://github.com/sheniaks)_

---

## 🔧 General Syntax
```bash
tcpdump [options] [expression]
```

---

## 🌐 HTTP Traffic

```bash
tcpdump -i any tcp port 80                          # Capture HTTP packets
tcpdump -A -i any tcp port 80                       # Show ASCII payload
tcpdump -A -r file.pcap | grep 'HTTP/1\.1'         # Filter HTTP responses
tcpdump -A -r file.pcap | grep -E '^(GET|POST)'     # Filter HTTP requests
grep -E '^(GET|POST)' file.txt | awk '{print $1}' | sort | uniq -c  # Count methods
```

---

## 🔒 SSH Traffic

```bash
tcpdump -i any port 22                              # Capture SSH
tcpdump -r ssh.pcap 'src port 22' | wc -l           # Count replies
tcpdump -r ssh.pcap 'dst port 22' | wc -l           # Count requests
```

🧮 Count both:
```bash
echo "Requests: $(tcpdump -r ssh.pcap 'dst port 22' | wc -l)"
echo "Replies:  $(tcpdump -r ssh.pcap 'src port 22' | wc -l)"
```

📝 From text:
```bash
grep '\.ssh >' ssh.txt | wc -l                     # SSH replies
grep '> .*\.ssh' ssh.txt | wc -l                   # SSH requests
```

---

## 📨 DNS Traffic

```bash
tcpdump -i any port 53                              # Capture DNS
tcpdump -r dns.pcap | grep 'A? ' | wc -l            # Count queries
tcpdump -r dns.pcap | grep ' A ' | grep -v 'A?' | wc -l  # Count responses
tcpdump -r dns.pcap | grep 'A? ' | awk '{print $NF}' | sort | uniq     # Domains
tcpdump -r dns.pcap | grep ' A ' | grep -v 'A?' | awk '{print $NF}' | sort | uniq  # Resolved IPs
tcpdump -n -r file.pcap port 53 and 'udp[10] & 0x80 = 0' | head -1      # First DNS query
```

---

## 💣 ICMP (Ping)

```bash
tcpdump -i any icmp                                  # Capture ICMP
tcpdump -r icmp.pcap icmp[icmptype] = 8 | wc -l      # Count Echo Requests
tcpdump -r icmp.pcap icmp[icmptype] = 0 | wc -l      # Count Echo Replies
```

---

## 🚩 TCP Flag Filters

```bash
tcpdump "tcp[tcpflags] == tcp-syn"                  # Only SYN
tcpdump "tcp[tcpflags] & tcp-syn != 0"              # SYN set
tcpdump "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"    # SYN or ACK
tcpdump -r file.pcap "tcp[tcpflags] == tcp-rst" -n | wc -l  # Count RST
```

---

## 🎯 Common Filter Expressions

```bash
tcpdump host 192.168.1.1
tcpdump src host 10.0.0.1
tcpdump dst host 8.8.8.8
tcpdump port 443
tcpdump src port 22
tcpdump dst port 53
```

---

## 📦 Find Large Packet Senders

```bash
tcpdump -nn -r traffic.pcap greater 15000
tcpdump -nn -r traffic.pcap greater 15000 | awk '{print $3}' | cut -d. -f1-4 | sort | uniq -c
```

---

## 🧬 Advanced Filtering: Byte Offset

```bash
ether[0] & 1 != 0                      # Multicast Ethernet
ip[0] & 0xF != 5                       # IP packets with options
```

---

## 📋 Display Format Options Summary

| Option    | Description                      |
|-----------|----------------------------------|
| `-q`      | Quiet output                     |
| `-e`      | Show link-layer headers (MAC)    |
| `-A`      | ASCII content only               |
| `-X`      | Hex + ASCII                      |
| `-xx`     | Hex only                         |

---

## 🧾 File Operations

```bash
tcpdump -r file.pcap                              # Read from pcap
tcpdump -i any -w capture.pcap                    # Write to pcap
tcpdump -tttt -r file.pcap                        # Human-readable timestamps
```
