# 📡 tcpdump Cheat Sheet for Security+ (SY0-701)

_By Oleksandr Sheniak — GitHub: [sheniaks](https://github.com/sheniaks)_

---

## 🔧 General Syntax
```bash
tcpdump [options] [expression]
```

---

## 🌐 HTTP Traffic

### 🔍 Capture HTTP Traffic (Port 80)
```bash
tcpdump -i any tcp port 80
```

### 📄 Show ASCII Payload (requests/responses)
```bash
tcpdump -A -i any tcp port 80
```

### ✅ Filter HTTP Requests (GET, POST, etc.)
```bash
tcpdump -A -r file.pcap | grep -E '^(GET|POST|HEAD|PUT|DELETE|OPTIONS|PATCH) '
```

### 📬 Filter HTTP Responses
```bash
tcpdump -A -r file.pcap | grep 'HTTP/1\.1'
```

### 🔢 Count HTTP Methods
```bash
grep -E '^(GET|POST|HEAD)' file.txt | awk '{print $1}' | sort | uniq -c
```

---

## 🔒 SSH Traffic

### 🛠️ Capture SSH (Port 22)
```bash
tcpdump -i any port 22
```

### ✅ Count Requests / Responses
```bash
tcpdump -r ssh.pcap 'src port 22' | wc -l    # Replies (server → client)
tcpdump -r ssh.pcap 'dst port 22' | wc -l    # Requests (client → server)
```

### 🔒 SSH Traffic — Client vs Server

- SSH **requests** are packets **to port 22** (client → server):
  ```bash
  tcpdump -r ssh.pcap 'dst port 22' | wc -l
  ```

- SSH **replies** are packets **from port 22** (server → client):
  ```bash
  tcpdump -r ssh.pcap 'src port 22' | wc -l
  ```

### 🧮 Count SSH Packets (Combined Output)
```bash
echo "SSH Requests: $(tcpdump -r ssh.pcap 'dst port 22' | wc -l)"
echo "SSH Replies:  $(tcpdump -r ssh.pcap 'src port 22' | wc -l)"
```

### 📝 grep-Based Count (from ASCII Text File)
```bash
grep '\.ssh >' ssh.txt | wc -l     # SSH replies
grep '> .*\.ssh' ssh.txt | wc -l   # SSH requests
```

### 🧠 SSH Count One-Liner:
```bash
tcpdump -r ssh.pcap 'tcp port 22' | awk '
  /.ssh >/ { replies++ }
  /> .*\.ssh/ { requests++ }
  END { print "Requests:", requests, "Replies:", replies }'
```

---

## 📨 DNS Traffic

### 🔍 Capture DNS Traffic (Port 53)
```bash
tcpdump -i any port 53
```

### 📬 Count DNS Requests
```bash
tcpdump -r dns.pcap | grep 'A? ' | wc -l
```

### 📤 Count DNS Responses
```bash
tcpdump -r dns.pcap | grep ' A ' | grep -v 'A?' | wc -l
```

### 🌍 Extract Domains
```bash
tcpdump -r dns.pcap | grep 'A? ' | awk '{print $NF}' | sort | uniq
```

### 🌐 Extract Resolved IPs
```bash
tcpdump -r dns.pcap | grep ' A ' | grep -v 'A?' | awk '{print $NF}' | sort | uniq
```

---

## 💣 ICMP (Ping)

### 🔍 Capture ICMP (Ping Requests/Replies)
```bash
tcpdump -i any icmp
```

### ✅ Count ICMP Echo Requests / Replies
```bash
tcpdump -r icmp.pcap icmp[icmptype] = 8 | wc -l   # Echo requests
tcpdump -r icmp.pcap icmp[icmptype] = 0 | wc -l   # Echo replies
```

---

## 📦 Extras

### 🧾 Read from Capture File
```bash
tcpdump -r file.pcap
```

### 💾 Save to PCAP
```bash
tcpdump -i any -w capture.pcap
```

### 📜 Timestamps in Readable Format
```bash
tcpdump -tttt -r file.pcap
```
