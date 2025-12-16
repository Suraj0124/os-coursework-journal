# Week 3: Performance Testing Application Selection

## Introduction

This week focuses on selecting and configuring specific applications to generate various workload types for performance evaluation on Ubuntu Server. Each application has been chosen based on its ability to generate consistent, measurable workloads that stress specific system resources. The selected tools will be installed via SSH and monitored using the strategy established in Week 2.

## Application Selection Matrix

| Workload Type | Application | Justification |
|---------------|-------------|---------------|
| **CPU-Intensive** | Sysbench | Mathematical consistency via prime number calculation; repeatable benchmark |
| **RAM-Intensive** | Stress-ng | Advanced memory allocation testing; evaluates swap performance |
| **I/O-Intensive** | Fio | Flexible I/O patterns; bypasses OS cache for true hardware speed measurement |
| **Network-Intensive** | iPerf3 | Pure TCP/UDP throughput testing; independent of disk performance |
| **Server Application** | Apache2 | Industry-standard web server for concurrent connection testing |

---

## 1. CPU-Intensive Workload

### Application: Sysbench

**Justification:** Sysbench generates mathematically consistent processor loads based on prime number calculations, providing stable and repeatable benchmarks ideal for CPU performance evaluation.

### Installation
```bash
# Install sysbench
sudo apt update
sudo apt install sysbench -y

# Verify installation
sysbench --version
```

![Sysbench Installation](assets/images-3/sysbench-install.png)

### Expected Resource Profile

- **CPU Usage:** 100% utilization across all cores
- **Load Average:** Equals vCPU count
- **Memory/Disk:** Minimal usage
- **Test Command:** `sysbench cpu --cpu-max-prime=20000 run`

### Test Execution

![Sysbench CPU Test](assets/images-3/sysbench-test.png)

![CPU Monitoring](assets/images-3/sysbench-monitoring.png)

---

## 2. RAM-Intensive Workload

### Application: Stress-ng

**Justification:** Stress-ng is an advanced version of the original stress tool, allowing aggressive cycle memory allocation testing to evaluate both physical memory and swap performance effectively.

### Installation
```bash
# Install stress-ng
sudo apt install stress-ng -y

# Verify installation
stress-ng --version
```

![Stress-ng Installation](assets/images-3/stress-ng-install.png)

### Expected Resource Profile

- **RAM Usage:** Spikes to physical memory limit
- **Swap Activity:** Heavy disk I/O when RAM exhausted
- **CPU Usage:** Increased due to kswapd process
- **Test Command:** `stress-ng --vm $(nproc) --vm-bytes 90% --timeout 20s`

### Test Execution

![Stress-ng RAM Test](assets/images-3/stress-ng-test.png)

![RAM Monitoring](assets/images-3/ram-monitoring.png)

---

## 3. I/O-Intensive Workload

### Application: Fio (Flexible I/O Tester)

**Justification:** Fio offers high flexibility and configurability to simulate specific I/O patterns found in database or file server environments. It can bypass the OS cache to measure true hardware speed.

### Installation
```bash
# Install fio
sudo apt install fio -y

# Verify installation
fio --version
```

![Fio Installation](assets/images-3/fio-install.png)

### Expected Resource Profile

- **Disk I/O:** Read/Write speeds reach hardware limits
- **CPU Usage:** High iowait percentage
- **Memory:** Moderate usage for buffers
- **Test Command:** `fio --name=test --rw=randwrite --bs=4k --size=1G --numjobs=4 --runtime=30 --group_reporting`

### Test Execution

![Fio Disk Test](assets/images-3/fio-test.png)

![Disk I/O Monitoring](assets/images-3/disk-monitoring.png)

---

## 4. Network-Intensive Workload

### Application: iPerf3

**Justification:** iPerf3 generates pure TCP/UDP traffic between client and server to accurately measure maximum throughput, jitter, and packet loss. It is completely independent of hard drive speed, focusing solely on network capacity.

### Installation
```bash
# Install iperf3 (on both server and desktop)
sudo apt install iperf3 -y

# Verify installation
iperf3 --version

# Configure firewall
sudo ufw allow 5201/tcp
```

![iPerf3 Installation](assets/images-3/iperf3-install.png)

### Expected Resource Profile

- **Network Throughput:** Plateaus at NIC/bandwidth limit
- **CPU/RAM Usage:** Low
- **Server Command:** `iperf3 -s`
- **Client Command:** `iperf3 -c <server_ip>`

### Test Execution

**Server Side (listening):**

![iPerf3 Server](assets/images-3/iperf-server.png)

**Client Side (testing):**

![iPerf3 Client](assets/images-3/iperf-client.png)

**Network Monitoring:**

![Network Monitoring](assets/images-3/network-monitoring.png)

---

## 5. Server Application Workload

### Application: Apache2 Web Server

**Justification:** Apache2 is an industry-standard web server that allows testing of concurrent connection handling, request processing, and overall server performance under realistic workloads.

### Installation
```bash
# Install Apache2
sudo apt install apache2 -y

# Verify installation
apache2 -v

# Check service status
sudo systemctl status apache2

# Install Apache Benchmark tool (for testing)
sudo apt install apache2-utils -y
```

![Apache2 Installation](assets/images-3/apache-install.png)

### Expected Resource Profile

- **CPU/RAM Usage:** Scales with concurrent connections
- **Network Throughput:** Increases under load
- **Disk I/O:** Moderate (serving files)
- **Test Command:** `ab -n 10000 -c 100 http://<server_ip>/`

### Test Execution

![Apache Benchmark Test](assets/images-3/apache-test.png)

![Server Monitoring](assets/images-3/server-monitoring.png)

---

## Monitoring Strategy

### Tools Used
- nmon (comprehensive resource monitoring)
- htop (process monitoring)
- iotop (disk I/O monitoring)
- iftop (network monitoring)

### Monitoring Approach

1. **Baseline Measurement:** Capture idle resource state before test execution
2. **Real-time Monitoring:** Run monitoring tools in separate SSH session during workload generation
3. **Peak Analysis:** Document maximum resource usage during tests
4. **Metric Correlation:** Cross-reference monitoring data with application-specific metrics

### Key Metrics by Workload

| Workload | Primary Metrics | Secondary Metrics |
|----------|----------------|-------------------|
| CPU | Utilization %, Load Average | User/System time, Context switches |
| RAM | Used/Free memory, Swap usage | Page faults, kswapd activity |
| Disk I/O | Read/Write speed (MB/s), IOPS | iowait %, Queue depth |
| Network | Throughput (Mbps), Latency | Packet loss, Jitter |
| Server | Requests/sec, Response time | Failed requests, Concurrent connections |

![Monitoring Overview](assets/images-3/monitoring-overview.png)

---

## Summary

All selected applications have been installed and configured for SSH-based performance testing. Each tool targets specific system resources with predictable workload patterns, enabling accurate performance evaluation and comparison between Ubuntu Server and Desktop environments.
* **Figure W3-9:** iperf3 client-side network throughput test.
* **Figure W3-10:** nmon network monitoring during iperf3 testing.
