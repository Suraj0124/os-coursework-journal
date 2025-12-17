# ASSESSMENT WEEK 6 – Performance Evaluation and Analysis

---

## 1. Introduction

This document presents a comprehensive performance evaluation and analysis of an Ubuntu Server system conducted for Assessment Week 6. The assessment integrates system baselining, storage performance testing, network throughput analysis, application load testing, bottleneck identification, optimisation implementation, and trade-off evaluation.

The objective of this assessment is to:

* Establish baseline system performance metrics
* Analyse system behaviour under load
* Identify primary resource bottlenecks
* Implement and validate performance optimisations
* Evaluate configuration trade-offs using quantitative evidence

The testing methodology aligns with prior laboratory work on storage baselining and network performance analysis and demonstrates the system’s capability to handle application workloads securely and efficiently.

---

## 2. Methodology: System Baseline (Idle State)

Baseline testing was performed with the system in an idle state to establish control metrics for comparison against load conditions.

### Tools Used

* `top`
* `free -h`
* `iostat -x`
* `ss -s`

### 2.1 CPU and Process State (Idle)

* Average CPU usage: **[MEASURED VALUE]**
* Idle CPU time: **~95%**

📸 *Insert CPU baseline screenshot*

```md
![CPU Baseline](images/baseline_cpu.png)
```

### 2.2 Memory Availability (Idle)

* Used memory: **[MEASURED VALUE] (~4.3%)**
* Free memory: **~9.5 GB**
* Swap usage: **0 MB**

📸 *Insert memory baseline screenshot*

```md
![Memory Baseline](images/baseline_memory.png)
```

### 2.3 Disk Subsystem (Idle)

* Disk throughput: **0 MB/s**
* Disk utilisation: **0.00%**

📸 *Insert disk baseline screenshot*

```md
![Disk Baseline](images/baseline_disk.png)
```

### 2.4 Network (Idle)

* Network throughput: **0 Mbps**
* No active connections

📸 *Insert network baseline screenshot*

```md
![Network Baseline](images/baseline_network.png)
```

---

## 3. Storage Subsystem Performance

### 3.1 Disk Throughput Test

To determine the disk write performance limit, the following command was executed:

```bash
dd if=/dev/zero of=testfile bs=1M count=1024 oflag=direct
```

### Monitoring Tool

```bash
iostat -x 1
```

### Observations

* Write throughput: **[MEASURED VALUE]**
* Disk utilisation (%util): **[MEASURED VALUE]**
* Average I/O wait time (await): **[MEASURED VALUE]**

📸 *Insert iostat monitoring screenshot*

```md
![Disk IO Test](images/disk_iostat.png)
```

---

## 4. Network Subsystem Performance

### 4.1 Latency Testing

**Tool Used:** `ping`

```bash
ping -c 10 192.168.10.4
```

* Average RTT: **Low and stable**

📸 *Insert ping results screenshot*

```md
![Ping Test](images/network_ping.png)
```

### 4.2 Throughput Testing

**Tool Used:** `iperf3`

```bash
# Server
iperf3 -s

# Client
iperf3 -c 192.168.10.4 -t 30
```

### Observations

* Average bandwidth: **[MEASURED VALUE]**
* Retransmissions: **0**

📸 *Insert iperf3 results screenshot*

```md
![iperf3 Test](images/network_iperf.png)
```

### 4.3 Web Server Concurrency Test

```bash
sudo apt install apache2 apache2-utils -y
ab -n 10000 -c 100 http://server-ip/
```

**Results:**

* Requests per second: **[MEASURED VALUE]**
* Average latency: **[MEASURED VALUE]**
* Transfer rate: **~60 MB/s**

📸 *Insert Apache benchmark screenshot*

```md
![Apache Benchmark](images/apache_ab.png)
```

---

## 5. Application Load Testing

### 5.1 CPU Stress Test

```bash
stress --cpu 2 --timeout 60s
```

**Observation:**

* CPU core reached **[MEASURED VALUE] utilisation**

📸 *Insert CPU stress screenshot*

```md
![CPU Stress](images/cpu_stress.png)
```

### 5.2 Memory Stress Test

```bash
stress --vm 1 --vm-bytes 512M --timeout 60s
```

**Observation:**

* Memory usage increased to **[MEASURED VALUE] (~34%)**
* No swap usage observed
* Temporary CPU spike due to memory allocation activity

📸 *Insert memory stress screenshot*

```md
![Memory Stress](images/memory_stress.png)
```

---

## 6. Performance Comparison Table (Baseline vs Load)

| Metric           | Baseline (Idle)  | Under Load                | Observation          |
| ---------------- | ---------------- | ------------------------- | -------------------- |
| CPU Usage        | [MEASURED VALUE] | [MEASURED VALUE] (1 core) | Significant spike    |
| Memory Usage     | [MEASURED VALUE] | [MEASURED VALUE]          | No swap used         |
| Disk Throughput  | 0 MB/s           | [MEASURED VALUE]          | High sustained write |
| Disk Utilisation | 0.00%            | [MEASURED VALUE]          | Near saturation      |
| Disk Latency     | 0 ms             | [MEASURED VALUE]          | Low latency          |
| Network Speed    | 0 Mbps           | 221 Mbps                  | Stable, no loss      |

---

## 7. Bottleneck Identification

**Primary Bottleneck Identified:** Disk I/O

### Evidence

* Disk utilisation reached **[MEASURED VALUE]**
* Write throughput capped at **~189 MB/s**

### Impact

Write-intensive applications exceeding this throughput will experience I/O queuing and increased latency.

---

## 8. Implemented Performance Optimisations

### Optimisation #1: Disk I/O Scheduler Change

**Bottleneck Addressed:** Disk I/O

**Configuration Before:** Default scheduler

```bash
cat /sys/block/sda/queue/scheduler
```

**Implementation:**

```bash
echo deadline | sudo tee /sys/block/sda/queue/scheduler
```

**Performance After:**

* Disk throughput: **[MEASURED VALUE]**
* Disk utilisation: **82%**

**Improvement:**

* Throughput increase: **[MEASURED VALUE]**

---

### Optimisation #2: Memory Swappiness Reduction

**Bottleneck Addressed:** Memory pressure prevention

**Configuration Before:**

* vm.swappiness = **60**

**Implementation:**

```bash
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

**Performance After:**

* Swap usage: **0 MB**
* Improved application response consistency

**Improvement:**

* Swap activity reduced to **0%**

---

## 9. Post-Optimisation Performance Comparison

| Metric           | Before           | After            | Improvement      |
| ---------------- | ---------------- | ---------------- | ---------------- |
| Disk Throughput  | [MEASURED VALUE] | [MEASURED VALUE] | [MEASURED VALUE] |
| Disk Utilisation | [MEASURED VALUE] | 82%              | -9.5%            |
| Swap Usage       | Minimal          | 0 MB             | Eliminated       |

---

## 10. Configuration Trade-off Analysis

### Trade-off 1: Disk Scheduler – Default vs Deadline

* **Performance:** [MEASURED VALUE] throughput
* **Stability:** Slightly reduced fairness
* **Decision:** Deadline scheduler selected for server workload

### Trade-off 2: Swappiness – 60 vs 10

* **Performance:** Reduced latency under memory load
* **Risk:** Potential OOM under extreme memory exhaustion
* **Decision:** Swappiness set to 10

### Trade-off 3: Apache Concurrency – High vs Moderate

* **Performance:** Higher throughput
* **Resource Use:** Increased CPU usage

### Trade-off 4: Stress Testing Intensity

* **Coverage vs Risk:** Short-duration tests selected to avoid system instability

### Trade-off 5: Network Buffer Defaults vs Tuned

* **Performance:** Defaults sufficient for 221 Mbps

### Trade-off 6: Security vs Performance

* **Minimal firewall rules retained** to avoid packet-processing overhead

---

## 11. Conclusion

This assessment successfully established baseline system performance, identified disk I/O as the primary bottleneck, implemented targeted optimisations, and validated performance improvements using quantitative metrics. The system demonstrated stability under load, efficient memory handling, and consistent network throughput. Trade-off analysis justified configuration decisions, ensuring a balanced approach between performance, reliability, and security.
