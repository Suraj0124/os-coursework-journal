# Phase 3: Application Selection for Performance Testing (Week 3)

## Introduction
In this phase, I selected and justified a set of applications to generate different system workloads for performance testing. The goal was to choose tools that are reliable, repeatable, and commonly used in real-world system evaluation. Each application targets a specific workload type—CPU, memory, disk I/O, network, and server workloads—allowing me to observe how system resources behave under varying stress conditions.

---

## Application Selection Overview

### CPU-Intensive Workload
**Selected Tool:** Sysbench  

I chose Sysbench because it produces a consistent and repeatable CPU load using prime number calculations. This makes it well suited for benchmarking processor performance across multiple test runs.

**Test Command:**
```
sysbench cpu --cpu-max-prime=20000 run
```

**Screenshot Placeholder:**  
*Figure 1: Sysbench CPU test execution and output*

---

### RAM-Intensive Workload
**Selected Tool:** Stress-ng  

Stress-ng was selected because it offers advanced memory stress options compared to the original stress tool. It allows aggressive memory allocation, making it effective for observing RAM exhaustion and swap activity.

**Test Command:**
```
stress-ng --vm $(nproc) --vm-bytes 90% --timeout 20s
```

**Screenshot Placeholder:**  
*Figure 2: Stress-ng memory workload output*

---

### I/O-Intensive Workload
**Selected Tool:** Fio  

Fio was chosen for its flexibility and ability to simulate realistic disk access patterns. It can bypass operating system caching, providing accurate measurements of raw disk performance.

**Example Test Command:**
```
fio --name=randwrite --rw=randwrite --bs=4k --size=1G --runtime=30 --group_reporting
```

**Screenshot Placeholder:**  
*Figure 3: Fio disk I/O test results*

---

### Network-Intensive Workload
**Selected Tool:** iPerf3  

I selected iPerf3 because it focuses exclusively on network performance. It generates TCP or UDP traffic between a client and server, allowing precise measurement of throughput, jitter, and packet loss.

**Server Command:**
```
iperf3 -s
```

**Client Command:**
```
iperf3 -c <server-ip>
```

**Screenshot Placeholder:**  
*Figure 4: iPerf3 client-server network test*

---

### Server-Based Workload
**Selected Tool:** Apache2  

Apache2 was included as a representative server application. It produces a mixed workload involving CPU, memory, disk, and network activity, closely reflecting real-world server usage.

**Screenshot Placeholder:**  
*Figure 5: Apache2 service status and request handling*

---

## Application Selection Matrix

| Workload Type | Application | Justification |
|---------------|------------|---------------|
| CPU | Sysbench | Provides consistent and repeatable CPU benchmarking |
| RAM | Stress-ng | Advanced memory stress and swap testing |
| Disk I/O | Fio | Flexible and realistic disk workload simulation |
| Network | iPerf3 | Accurate measurement of network throughput |
| Server | Apache2 | Represents real-world mixed server workloads |

---

## Installation Documentation (SSH-Based)

All tools were installed via SSH on a Linux system using the `apt` package manager.

```
sudo apt update
sudo apt install sysbench stress-ng fio iperf3 apache2 -y
```

**Verification Example:**
```
sysbench --version
stress-ng --version
fio --version
iperf3 --version
```

**Screenshot Placeholder:**  
*Figure 6: Package installation and version verification*

---

## Expected Resource Profiles

### CPU Workload (Sysbench)
CPU utilization is expected to reach 100% across all available cores. The system load average should increase accordingly, while memory and disk usage remain minimal.

**Screenshot Placeholder:**  
*Figure 7: CPU utilization during Sysbench test*

---

### RAM Workload (Stress-ng)
Memory usage should rapidly increase until physical RAM is exhausted. Disk activity is expected to rise due to swapping, and kernel processes may consume additional CPU time.

**Screenshot Placeholder:**  
*Figure 8: Memory and swap usage during Stress-ng test*

---

### Disk I/O Workload (Fio)
Disk read/write throughput is expected to reach the limits of the storage device. CPU usage will likely appear as increased I/O wait time.

**Screenshot Placeholder:**  
*Figure 9: Disk I/O and iowait statistics during Fio test*

---

### Network Workload (iPerf3)
Network throughput should plateau at the NIC or bandwidth limit. CPU and memory usage are expected to remain relatively low.

**Screenshot Placeholder:**  
*Figure 10: Network throughput monitoring during iPerf3 test*

---

## Monitoring Strategy
System performance was monitored using tools introduced in Week 2, including `top`, `htop`, `vmstat`, `iostat`, and `nmon`. Each workload was executed independently while observing real-time resource usage to identify bottlenecks and system behavior under stress.

**Screenshot Placeholder:**  
*Figure 11: System monitoring output during workload execution*

---

## Reflection
This phase improved my understanding of how different workloads impact specific system resources. Selecting appropriate tools allowed me to isolate CPU, memory, disk, and network behavior more effectively. These observations will be valuable in later phases when analyzing performance bottlenecks and optimizing system configurations.
