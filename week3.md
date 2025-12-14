
# Week 3: Application Selection for Performance Testing

## Overview

**Phase:** Application Selection for Performance Testing
**Module:** CMPN202 – Operating Systems
**Focus:** Selecting representative applications to evaluate OS performance under different workload types and planning how they will be monitored and analysed.

---

## Objectives for This Week

* Identify applications that represent different workload categories
* Justify application choices based on OS behaviour and resource usage
* Document installation steps using SSH and CLI only
* Predict expected resource utilisation patterns
* Define a clear monitoring and measurement strategy

---

## Selected Workload Categories

The following workload types are considered in this coursework:

* CPU-intensive
* Memory (RAM)-intensive
* Disk / I/O-intensive
* Network-intensive
* Server / service-based workload

---

## Application Selection Matrix

| Workload Type      | Application            | Description                      | Justification                                                                                     |
| ------------------ | ---------------------- | -------------------------------- | ------------------------------------------------------------------------------------------------- |
| CPU-intensive      | stress-ng              | CPU stress testing tool          | Generates controlled CPU load to evaluate scheduling, load handling, and CPU saturation behaviour |
| RAM-intensive      | stress-ng (vm workers) | Memory stress testing            | Allocates and stresses RAM to analyse memory management, swapping, and paging behaviour           |
| I/O-intensive      | fio                    | Flexible I/O tester              | Simulates realistic disk read/write workloads to evaluate filesystem and disk I/O performance     |
| Network-intensive  | iperf3                 | Network performance testing tool | Measures bandwidth, throughput, and latency over TCP/UDP connections                              |
| Server application | nginx                  | Lightweight web server           | Represents real-world server workload handling concurrent client requests                         |

*Table 1: Selected applications mapped to workload categories with justification.*

---

## Installation Documentation (via SSH)

![Figure 3.1 SSH Login](assets/week3/week3_ssh_login.png)

**Figure 3.1:** Successful SSH connection from Ubuntu Desktop workstation to Ubuntu Server, demonstrating remote command-line administration.

This screenshot confirms successful remote administration of the server via SSH.

---

## Installed Applications

![Figure 3.2 Application Installations](assets/week3/week3_app_installations.png)

**Figure 3.2:** Installation of performance testing applications (`stress-ng`, `sysbench`, `fio`, `iperf3`, and `nginx`) on Ubuntu Server using APT via SSH.

These commands install tools representing different workload types using SSH on a headless server.

---

## Application Verification

![Figure 3.3 Application Versions](assets/week3/week3_app_versions.png)

**Figure 3.3:** Version verification confirming successful installation of all selected performance testing applications.

Version checks confirm that all applications are installed and ready for testing.

---

## Monitoring Strategy

### Baseline Measurement

![Figure 3.4 Baseline System Usage](assets/week3/week3_baseline_top.png)

**Figure 3.4:** Baseline system resource usage on Ubuntu Server before executing any performance workloads.

This baseline provides a reference point for comparison during workload execution.

### CPU Workload Observation

![Figure 3.5 stress-ng CPU Load](assets/week3/week3_stressng_cpu.png)

**Figure 3.5:** CPU utilisation during `stress-ng` execution, demonstrating processor saturation under a controlled workload.

This demonstrates CPU saturation during a controlled workload.

---

## Server Application Evidence

![Figure 3.6 nginx Service Status](assets/week3/week3_nginx_status.png)

**Figure 3.6:** nginx web server running successfully as a representative server-based workload.

nginx is used as a representative server-based workload.

---

## Expected Resource Profiles

This section documents the anticipated system behaviour before testing.

| Application     | CPU Usage    | Memory Usage | Disk I/O      | Network Usage | Notes                      |   |
| --------------- | ------------ | ------------ | ------------- | ------------- | -------------------------- | - |
| stress-ng (CPU) | Very High    | Low          | Minimal       | None          | Saturates CPU cores        |   |
| stress-ng (RAM) | Moderate     | Very High    | Possible swap | None          | Tests memory pressure      |   |
| fio             | Low–Moderate | Low          | Very High     | None          | Disk read/write focus      |   |
| iperf3          | Moderate     | Low          | Minimal       | Very High     | Network throughput testing |   |
| nginx           | Low–Moderate | Low–Moderate | Low           | Moderate–High | Simulates server workload  |   |
|                 |              |              |               |               |                            |   |

*Predictions are based on documentation, prior knowledge, and workload characteristics.*

---

## Monitoring Strategy

The following tools and metrics will be used to monitor system performance:

### Monitoring Tools

* `top` / `htop` – CPU and memory usage
* `free -h` – Memory statistics
* `iostat` – Disk I/O performance
* `vmstat` – System load and memory
* `iftop` / `ss` – Network activity
* `uptime` – Load averages

### Measurement Approach

* Baseline system measurements taken before application execution
* Measurements recorded during application execution
* Consistent sampling intervals used for fair comparison

---

## Evidence Collected

* SSH terminal screenshots showing installation commands
* CLI outputs demonstrating installed applications
* Initial monitoring screenshots (idle vs active states)

---

## Reflection (Week 3)

This week focused on understanding how different workload types stress various operating system components. Selecting appropriate applications highlighted the importance of matching workloads to measurable OS behaviours. The planning carried out this week forms the foundation for performance testing and optimisation in later phases.

---

## Next Week Preview (Week 4)

* Implement SSH key-based authentication
* Configure firewall rules
* Create non-root administrative user
* Begin secure remote administration

## Image Captions (Concise)

* **Figure W3-1:** Sysbench version output confirming successful installation.
* **Figure W3-2:** stress-ng version output confirming successful installation.
* **Figure W3-3:** fio version output confirming successful installation.
* **Figure W3-4:** iperf3 version output confirming successful installation.
* **Figure W3-5:** Sysbench CPU test command execution and output.
* **Figure W3-6:** stress-ng memory workload execution and output.
* **Figure W3-7:** fio disk I/O workload evidence during execution.
* **Figure W3-8:** iperf3 server listening state on Ubuntu Server.
* **Figure W3-9:** iperf3 client-side network throughput test.
* **Figure W3-10:** nmon network monitoring during iperf3 testing.
