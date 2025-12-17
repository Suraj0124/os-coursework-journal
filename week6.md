# Week 6: Performance Evaluation and Analysis

## 1. Introduction

This document outlines the performance testing methodology and results for Week 6. The objective is to identify resource bottlenecks and analyze operating system behavior under different workloads through systematic baseline measurements and load testing.

**Testing Environment:**
- **Server:** 192.168.56.103
- **Username:** Suraj
- **SSH Connection:** `ssh Suraj@192.168.56.103`
- All testing conducted remotely via SSH from workstation

---

## 2. System Baseline Methodology

Establishing control metrics for the system in idle state to allow accurate comparison during load testing.

**Tools Used:** `top`, `free`, `iostat`, `vmstat`, `uptime`

### Server Idle State

#### CPU and Process State

**Command:**
```bash
ssh Suraj@192.168.56.103 "top -bn1 | head -15"
```

**Screenshot:**

[Screenshot: top command showing idle CPU state]

**Baseline CPU Metrics:**
- CPU User %: [value]
- CPU System %: [value]
- CPU Idle %: [value]
- Load Average (1min): [value]
- Load Average (5min): [value]

---

#### Memory Availability

**Command:**
```bash
ssh Suraj@192.168.56.103 "free -h"
```

**Screenshot:**

[Screenshot: free -h output showing available memory]

**Baseline Memory Metrics:**
- Total Memory: [value]
- Used Memory: [value] ([%])
- Available Memory: [value] ([%])
- Swap Used: [value]

---

#### Disk Subsystem Idle State

**Command:**
```bash
ssh Suraj@192.168.56.103 "iostat -x 1 5"
```

**Screenshot:**

[Screenshot: iostat showing minimal disk activity]

**Baseline Disk Metrics:**
- Read Speed: [value] MB/s
- Write Speed: [value] MB/s
- %util: [value]%
- await: [value] ms

---

#### Network Baseline

**Command:**
```bash
ssh Suraj@192.168.56.103 "ip -s link show"
```

**Screenshot:**

[Screenshot: network interface statistics]

---

## 3. Storage Subsystem Performance

### Disk Write Speed Test

**Command:**
```bash
ssh Suraj@192.168.56.103 "dd if=/dev/zero of=testfile bs=1M count=1024 oflag=direct"
```

**Screenshot:**

[Screenshot: dd write test output]

**Write Test Results:**
- Bytes Written: [value] bytes
- Write Speed: [value] MB/s
- Time Taken: [value] seconds

---

### Monitoring During Write Test

**Command:**
```bash
ssh Suraj@192.168.56.103 "iostat -x 1 10"
```

**Screenshot:**

[Screenshot: iostat during dd test showing high utilization]

**Observations:**

| Metric | Idle State | During Write | Change |
|--------|------------|--------------|--------|
| Write Speed (MB/s) | 0.00 | [value] | +[value] |
| %util | 0.00% | [value]% | +[value]% |
| await (ms) | 0.00 | [value] | +[value] |

**Key Findings:**
- %util reached [value]% - near disk saturation
- await of [value] ms - low latency despite high load
- Write throughput: [value] MB/s maximum sustained speed

---

## 4. Network Subsystem Performance

### Latency Testing

**Tool Used:** `ping` (ICMP)

**Command:**
```bash
ping -c 10 192.168.56.103
```

**Screenshot:**

[Screenshot: ping output with RTT statistics]

**Latency Results:**
- Packets Transmitted: 10
- Packets Received: [value]
- Packet Loss: [value]%
- Min/Avg/Max RTT: [value]/[value]/[value] ms

---

### Throughput Testing

**Tool Used:** `iperf3`

**Installation:**
```bash
ssh Suraj@192.168.56.103 "sudo apt install iperf3 -y"
```

**Server Side:**
```bash
ssh Suraj@192.168.56.103 "iperf3 -s -D"
```

**Screenshot:**

[Screenshot: iperf3 server listening]

---

**Client Side:**
```bash
iperf3 -c 192.168.56.103 -t 30
```

**Screenshot:**

[Screenshot: iperf3 throughput test results]

**Throughput Results:**
- Bandwidth: [value] Mbits/sec
- Retransmissions: [value]
- Test Duration: 30 seconds

**Key Findings:**
- Bandwidth: [value] Mbits/sec average transmission speed
- Retransmissions: [value] - [no packet loss/some loss observed]

---

## 5. Web Server Concurrency Test

### Installation

**Command:**
```bash
ssh Suraj@192.168.56.103 "sudo apt install apache2 apache2-utils -y"
ssh Suraj@192.168.56.103 "sudo systemctl start apache2"
```

**Screenshot:**

[Screenshot: Apache status showing active]

---

### Load Test

**Command:**
```bash
ab -n 10000 -c 100 http://192.168.56.103/
```

**Screenshot:**

[Screenshot: Apache Bench test output]

**Observation:**

| Metric | Value |
|--------|-------|
| Requests per Second | [value] |
| Time per Request | [value] ms |
| Failed Requests | [value] |
| Transfer Rate | [value] KB/s |

---

## 6. Application Load Testing

### CPU Stress Test

**Tool Used:** `stress`

**Installation:**
```bash
ssh Suraj@192.168.56.103 "sudo apt install stress -y"
```

**Command:**
```bash
ssh Suraj@192.168.56.103 "stress --cpu 2 --timeout 60s"
```

**Screenshot:**

[Screenshot: stress command with CPU at 100%]

**Observation:**

| Metric | Baseline | Under Stress | Change |
|--------|----------|--------------|--------|
| CPU Usage | [value]% | [value]% | +[value]% |
| Load Average | [value] | [value] | +[value] |

**Key Finding:** The stress process saturated the processor, reaching 100% CPU usage on stressed cores.

---

### Memory Stress Test

**Command:**
```bash
ssh Suraj@192.168.56.103 "stress --vm 1 --vm-bytes 512M --timeout 60s"
```

**Screenshot:**

[Screenshot: Memory usage spike during stress test]

**Observation:**

| Metric | Baseline | Under Stress | Change |
|--------|----------|--------------|--------|
| Used Memory | [value] MB | [value] GB | +[value] GB |
| Swap Used | [value] MB | [value] MB | +[value] MB |

**Key Finding:** Memory usage spiked to [value] GB. System handled spike without using swap. CPU spike observed due to memory allocation/deallocation overhead.

---

## 7. Performance Comparison Table

| Metric | Baseline (Idle) | Under Stress (Load) | Change / Observation |
|--------|----------------|---------------------|----------------------|
| CPU Usage | [value]% | [value]% | Significant spike. Core saturation achieved. |
| Memory Usage | [value] MB | [value] GB | +[value] GB. No swap used. |
| Disk Throughput | 0 MB/s | [value] MB/s | High sustained write speeds. |
| Disk Saturation | 0.00% | [value]% | Near saturation. |
| Disk Latency | 0 ms | [value] ms | Low latency despite high load. |
| Network Speed | 0 Mbps | [value] Mbps | Stable with 0 retransmissions. |

---

## 8. Bottleneck Identification

### Primary Bottleneck: Disk Write Performance

**Evidence:** During the disk write test, device saturation (%util) reached [value]%, and write throughput capped at [value] MB/s.

**Impact:** Storage system is the limiting factor for write-heavy operations. Applications requiring faster writes will experience queuing and delays.

**Screenshot:**

[Screenshot: iostat showing disk saturation]

---

## 9. Bottleneck Mitigation Strategies

### Strategy 1: Upgrade Storage Hardware
Replace current drive with high-performance NVMe SSD to increase write throughput beyond [current limit] MB/s.

### Strategy 2: Implement Caching
Configure write-back cache using available RAM to absorb write spikes before hitting disk.

### Strategy 3: I/O Distribution
Separate OS and application logs onto different physical disks to reduce contention.

---

## 10. Critical Analysis

### Performance Trade-offs
[Analyze trade-offs observed during testing]

### OS Behavior Observations
[Document how OS responded under different loads]

### Remote Administration Insights
[Reflect on SSH-based testing methodology]

---

## 11. Lessons Learned

### Technical Insights
- [Key insight 1]
- [Key insight 2]
- [Key insight 3]

### Challenges Encountered
- [Challenge and solution]

---

**Navigation:**
- [← Week 5](week5.md)
- [Week 7 →](week7.md)
- [Home](index.md)rity.
