# Week 5: Advanced Security Implementation Lab Report

**Operating Systems Coursework Journal**  
**Date:** December 18, 2025  
**Student:** Suraj  

---

## Table of Contents
1. [Part 1: Mandatory Access Control Implementation](#part-1-mandatory-access-control-implementation)
2. [Part 2: Intrusion Detection with fail2ban](#part-2-intrusion-detection-with-fail2ban)
3. [Part 3: Security Verification and Monitoring Scripts](#part-3-security-verification-and-monitoring-scripts)
4. [Conclusion](#conclusion)

---

## Part 1: Mandatory Access Control Implementation

### Task 1.1: Identifying Access Control System

Ubuntu systems typically use **AppArmor** as their Mandatory Access Control (MAC) system, while Red Hat-based systems use **SELinux**.

#### Checking AppArmor Status
```bash
sudo aa-status
```

![AppArmor Status](aa-status)

**Analysis:** The system shows multiple AppArmor profiles loaded and active, providing mandatory access control for various system services.

---

### Task 1.2: Working with AppArmor Profiles

#### Viewing Profile Modes

**Enforced Profiles:**
```bash
sudo aa-status --enforced
```
![Enforced Profiles](sudo-aa-status--enforced)

**Complain Mode Profiles:**
```bash
sudo aa-status --complaining
```
![Complaining Profiles](sudo-aa-status--complaining)

**Profiled Services:**
```bash
sudoaa-status-profiled
```
![Profiled Services](sudoaa-status-profiled)

#### Understanding Profile Modes

- **Enforce Mode:** Violations are actively blocked and logged. This provides active protection by preventing unauthorized access attempts.
- **Complain Mode:** Violations are logged but allowed to proceed. This is useful for testing and developing profiles without breaking functionality.

#### Examining Specific Profiles

**tcpdump Profile:**
```bash
sudo cat /etc/apparmor.d/usr.sbin.tcpdump
```
![tcpdump Profile](usr.sbin.tcpdump)

This profile defines the specific permissions and file access rules for the tcpdump network monitoring tool.

---

### Task 1.3: Creating an AppArmor Status Report Script

#### Script Implementation: `apparmor-report.sh`

![AppArmor Report Script](apparmor-report.sh)

**Script Breakdown:**

```bash
#!/bin/bash
# AppArmor Status Report Script
# Reports on all AppArmor profiles and their status

# Header section with timestamp and hostname
echo "========================================="
echo "AppArmor Status Report"
echo "========================================="
echo "Generated: $(date)"
echo "Hostname: $(hostname)"

# Verification that AppArmor is installed
if ! command -v aa-status &> /dev/null; then
    echo "ERROR: AppArmor is not installed"
    exit 1
fi

# Count and display total profiles
total_profiles=$(sudo aa-status --profiled | wc -l)
echo "Total profiles loaded: $total_profiles"

# List enforced profiles
sudo aa-status --enforced
enforced_count=$(sudo aa-status --enforced | wc -l)
echo "Count: $enforced_count"

# List complain mode profiles
sudo aa-status --complaining
complain_count=$(sudo aa-status --complaining | wc -l)
echo "Count: $complain_count"
```

**Script Execution:**

![AppArmor Report Output](nano-apparmor-report.sh)

**Key Features:**
- Checks for AppArmor installation
- Counts total, enforced, and complain-mode profiles
- Provides timestamped reporting
- Error handling for missing AppArmor

---

## Part 2: Intrusion Detection with fail2ban

### Task 2.1: Installing and Configuring fail2ban

#### Installation Verification

![fail2ban Installation](installfail2)

```bash
sudo apt update
sudo apt install fail2ban
sudo systemctl status fail2ban
```

#### Configuration Setup

Creating local configuration file:
```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```

**SSH Jail Configuration:**

![fail2ban Configuration](sudo-cat-etcaptaptconf.d50unattended-upgrades)

```ini
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 600
findtime = 600
```

**Parameter Explanation:**
- **maxretry = 3:** Ban after 3 failed login attempts
- **bantime = 600:** Ban duration of 600 seconds (10 minutes)
- **findtime = 600:** Time window for counting failures (10 minutes)

**Security Rationale:** These settings provide a balance between security and usability. Three attempts allow for legitimate typos while preventing brute-force attacks. The 10-minute ban window is sufficient to slow down automated attacks without permanent blocking.

---

### Task 2.2: Monitoring fail2ban Activity

#### Checking Active Jails

```bash
sudo fail2ban-client status
sudo fail2ban-client status sshd
```

![fail2ban Status](sudo-fail2ban-client-status-sshd)

**Analysis:** The output shows the SSH jail is active and monitoring authentication attempts. Currently, no IPs are banned, indicating no recent brute-force attempts.

#### System Service Status

![fail2ban Service Status](sudo-systemctl-restart-and-enable-fail2ban)

The service is configured to:
- Start automatically on boot (enabled)
- Run continuously (active/running)
- Monitor specified log files for intrusion attempts

#### Viewing Recent Activity

```bash
sudo tail -30 /var/log/fail2ban.log
```

![fail2ban Logs](sudo-tail-30-varlogfail2banlog)

**Log Analysis:** The logs show fail2ban initialization, jail activation, and normal monitoring activity. No ban actions are present, indicating the system is secure.

---

### Task 2.3: Configuring Automatic Security Updates

#### Installation and Configuration

```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades
```

![Unattended Upgrades Installation](installunattended-upgrades)
![Configuration Dialog](sudo-dpkg-reconfigure-ure--plow-unattended-upgrades)

#### Verification

**Service Status:**
```bash
sudo systemctl status unattended-upgrades
```

![Service Status](udocat-varlogunattended-upgradesunattended-upgradesun-attended-upg...)

**Configuration Review:**
```bash
sudo cat /etc/apt/apt.conf.d/50unattended-upgrades
```

![Configuration File](unattended-upgrades)

**Security vs Stability Trade-off:**

**Advantages:**
- Immediate patching of security vulnerabilities
- Reduces attack window for known exploits
- Ensures compliance with security policies
- Reduces administrative overhead

**Disadvantages:**
- Potential for updates to break applications
- Unexpected system changes
- Possible downtime from kernel updates requiring reboots
- Less control over update timing

**Recommendation:** Enable automatic security updates for production systems but implement proper monitoring and have rollback procedures in place.

---

## Part 3: Security Verification and Monitoring Scripts

### Task 3.1: Security Baseline Verification Script

#### Script Implementation: `security-baseline.sh`

![Security Baseline Script - Part 1](nano-security-baseline.sh)
![Security Baseline Script - Part 2](nano-security-baseline.sh)

**Complete Script with Detailed Comments:**

```bash
#!/bin/bash
# Security Baseline Verification Script
# Verifies all security configurations from Phase 05 and Phase 06
# This script runs on the server (executed via SSH)

echo "=========================================="
echo "Security Baseline Verification Report"
echo "=========================================="
echo "Generated: $(date)"
echo "Hostname: $(hostname)"
echo "Server IP: $(hostname -I | awk '{print $1}')"
echo ""

# Color codes for enhanced output readability
RED='\033[0;31m'      # For failures/warnings
GREEN='\033[0;32m'    # For successful checks
YELLOW='\033[1;33m'   # For informational warnings
NC='\033[0m'          # No Color - reset

# === SSH SECURITY CONFIGURATION ===
echo "=== SSH Security Configuration ==="

# Verify password authentication is disabled (best practice)
echo -n "Password Authentication: "
if grep -q "^PasswordAuthentication no" /etc/ssh/sshd_config; then
    echo -e "${GREEN}DISABLED${NC} (Secure)"
else
    echo -e "${RED}ENABLED${NC} (Warning: Should be disabled)"
fi

# Verify root login is disabled (critical security control)
echo -n "Root Login via SSH: "
if grep -q "^PermitRootLogin no" /etc/ssh/sshd_config; then
    echo -e "${GREEN}DISABLED${NC} (Secure)"
else
    echo -e "${RED}ENABLED${NC} (Warning: Should be disabled)"
fi

# Verify public key authentication is enabled
echo -n "Public Key Authentication: "
if grep -q "^PubkeyAuthentication yes" /etc/ssh/sshd_config; then
    echo -e "${GREEN}ENABLED${NC} (Secure)"
else
    echo -e "${YELLOW}DISABLED${NC} (Warning: Should be enabled)"
fi

# === FIREWALL CONFIGURATION ===
echo "=== Firewall Configuration ==="
if command -v ufw &> /dev/null; then
    echo "Firewall Status:"
    sudo ufw status
    echo ""
    echo "Active Rules:"
    sudo ufw status numbered
else
    echo -e "${RED}UFW not installed${NC}"
fi

# === INTRUSION DETECTION ===
echo "=== Intrusion Detection (fail2ban) ==="
if systemctl is-active --quiet fail2ban; then
    echo -e "Service Status: ${GREEN}RUNNING${NC} (Secure)"
    echo ""
    echo "Active Jails:"
    sudo fail2ban-client status
    echo ""
    echo "SSH Jail Status:"
    sudo fail2ban-client status sshd 2>/dev/null
else
    echo -e "Service Status: ${RED}NOT RUNNING${NC} (Warning)"
fi

# === MANDATORY ACCESS CONTROL ===
echo "=== Mandatory Access Control ==="
if command -v aa-status &> /dev/null; then
    echo "System: AppArmor"
    echo "Status:"
    sudo aa-status --profiled 2>/dev/null | head -5
    echo ""
    enforced=$(sudo aa-status --enforced 2>/dev/null | wc -l)
    echo "Profiles in enforce mode: $enforced"
elif command -v sestatus &> /dev/null; then
    echo "System: SELinux"
    sestatus | grep "SELinux status"
    sestatus | grep "Current mode"
else
    echo -e "${RED}No MAC system detected${NC} (Warning)"
fi

# === ADMINISTRATIVE USERS ===
echo "=== Administrative Users ==="
echo "Users with sudo privileges:"
getent group sudo | cut -d: -f4

# === AUTOMATIC SECURITY UPDATES ===
echo "=== Automatic Security Updates ==="
if dpkg -l | grep -q unattended-upgrades; then
    echo -e "unattended-upgrades: ${GREEN}INSTALLED${NC}"
    if systemctl is-enabled unattended-upgrades &> /dev/null; then
        echo -e "Status: ${GREEN}ENABLED${NC} (Secure)"
    else
        echo -e "Status: ${YELLOW}DISABLED${NC} (Warning)"
    fi
else
    echo -e "unattended-upgrades: ${RED}NOT INSTALLED${NC} (Warning)"
fi

# === SYSTEM RESOURCES ===
echo "=== System Resources ==="
echo "Memory Usage:"
free -h | grep -E "Mem|Swap"
echo ""
echo "Disk Usage:"
df -h | grep -E "^/dev"

echo "=========================================="
echo "Security Baseline Verification Complete"
echo "=========================================="
```

#### Script Execution and Output

![Script Output - Part 1](.monitor-server.sh)
![Script Output - Part 2](sshd-jail2)

**Security Verification Results:**
- ✅ SSH password authentication: Disabled
- ✅ SSH root login: Disabled
- ✅ Public key authentication: Enabled
- ✅ Firewall (UFW): Active with proper rules
- ✅ fail2ban: Running with SSH jail active
- ✅ AppArmor: Active with multiple enforced profiles
- ✅ Automatic updates: Installed and enabled

---

### Task 3.2: Remote Monitoring Script

#### Script Implementation: `monitor-server.sh`

![Monitor Server Script](nano-monitor-server.sh)

**Complete Script with Detailed Comments:**

```bash
#!/bin/bash
# Remote Server Monitoring Script
# Runs on workstation, connects to server via SSH
# Collects performance and security metrics

# === CONFIGURATION ===
SERVER="Suraj@192.168.56.103"  # Update with actual server details
LOGFILE="monitoring-$(date +%Y%m%d_%H%M%S).log"

# Color codes for output
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m'

echo "=========================================="
echo "Remote Server Monitoring Report"
echo "=========================================="
echo "Generated: $(date)"
echo "Monitoring: $SERVER"
echo "Log file: $LOGFILE"
echo ""

# Function to collect comprehensive metrics
collect_metrics() {
    # System identification
    echo "=== System Information ===" | tee -a $LOGFILE
    ssh $SERVER 'uname -a' | tee -a $LOGFILE
    echo "" | tee -a $LOGFILE
    
    # System uptime and load averages
    echo "=== Uptime and Load ===" | tee -a $LOGFILE
    ssh $SERVER 'uptime' | tee -a $LOGFILE
    echo "" | tee -a $LOGFILE
    
    # CPU usage - top 10 most intensive processes
    echo "=== CPU Usage (top 10 processes) ===" | tee -a $LOGFILE
    ssh $SERVER 'ps aux --sort=-%cpu | head -11' | tee -a $LOGFILE
    echo "" | tee -a $LOGFILE
    
    # Memory utilization
    echo "=== Memory Usage ===" | tee -a $LOGFILE
    ssh $SERVER 'free -h' | tee -a $LOGFILE
    echo "" | tee -a $LOGFILE
    
    # Disk space usage
    echo "=== Disk Usage ===" | tee -a $LOGFILE
    ssh $SERVER 'df -h' | tee -a $LOGFILE
    echo "" | tee -a $LOGFILE
    
    # Disk I/O statistics (requires sysstat package)
    echo "=== Disk I/O Statistics ===" | tee -a $LOGFILE
    ssh $SERVER 'iostat -x 1 2 | tail -10' 2>/dev/null | tee -a $LOGFILE
    if [ $? -ne 0 ]; then
        echo "iostat not available (install sysstat package)" | tee -a $LOGFILE
    fi
    echo "" | tee -a $LOGFILE
    
    # Network connection summary
    echo "=== Network Connections ===" | tee -a $LOGFILE
    ssh $SERVER 'ss -s' | tee -a $LOGFILE
    echo "" | tee -a $LOGFILE
    
    # Active network connections
    echo "=== Active Network Connections (top 10) ===" | tee -a $LOGFILE
    ssh $SERVER 'ss -tupn | head -11' | tee -a $LOGFILE
    echo "" | tee -a $LOGFILE
    
    # Security: Recent failed login attempts
    echo "=== Recent Failed Login Attempts ===" | tee -a $LOGFILE
    ssh $SERVER 'sudo grep "Failed password" /var/log/auth.log 2>/dev/null | tail -5' | tee -a $LOGFILE
    echo "" | tee -a $LOGFILE
    
    # fail2ban status
    echo "=== fail2ban Status ===" | tee -a $LOGFILE
    ssh $SERVER 'sudo fail2ban-client status 2>/dev/null' | tee -a $LOGFILE
    if [ $? -ne 0 ]; then
        echo "fail2ban not available" | tee -a $LOGFILE
    fi
    echo "" | tee -a $LOGFILE
}

# Test SSH connectivity before attempting monitoring
echo -n "Testing SSH connection... "
if ssh -o ConnectTimeout=5 $SERVER 'exit' 2>/dev/null; then
    echo -e "${GREEN}SUCCESS${NC}"
    echo ""
    collect_metrics
else
    echo -e "${YELLOW}FAILED${NC}"
    echo "Cannot connect to $SERVER"
    echo "Please check:"
    echo "1. Server is running"
    echo "2. SSH service is active"
    echo "3. Firewall allows connection from this IP"
    echo "4. SSH key authentication is configured"
    exit 1
fi

echo "=========================================="
echo "Monitoring Complete"
echo "=========================================="
echo "Full log saved to: $LOGFILE"
```

#### Script Execution

![Monitoring Script Execution](.monitor-server.sh)

**Monitoring Capabilities:**
- System identification and uptime
- CPU usage by process
- Memory and swap utilization
- Disk space and I/O statistics
- Network connection statistics
- Security event monitoring (failed logins)
- fail2ban intrusion detection status
- Automated log file generation with timestamps

---

## Conclusion

### Key Accomplishments

This lab successfully implemented comprehensive security controls across multiple layers:

1. **Mandatory Access Control (MAC)**
   - Implemented AppArmor with multiple enforced profiles
   - Created automated reporting script for MAC status
   - Verified profile configurations and modes

2. **Intrusion Detection**
   - Configured fail2ban for SSH brute-force protection
   - Set optimal thresholds (3 attempts, 10-minute ban)
   - Implemented automated monitoring

3. **Automatic Security Updates**
   - Enabled unattended-upgrades for security patches
   - Balanced security needs with stability requirements
   - Configured automated update verification

4. **Security Monitoring**
   - Created comprehensive baseline verification script
   - Implemented remote monitoring capabilities
   - Established automated logging and reporting

### Security Posture

The system now has multiple defensive layers:
- **Prevention:** SSH hardening, firewall rules, MAC policies
- **Detection:** fail2ban, system monitoring, audit logging
- **Response:** Automated banning, alert generation, logging
- **Maintenance:** Automatic security updates, regular verification

### Mandatory vs Discretionary Access Control

**Discretionary Access Control (DAC):**
- Traditional Linux file permissions (owner, group, others)
- Users control access to their own files
- Based on user identity and file ownership
- Example: `chmod 755 file.txt`

**Mandatory Access Control (MAC):**
- System-wide security policy enforced by the kernel
- Users cannot override security policy
- Based on labels and security contexts
- Example: AppArmor profiles, SELinux contexts

**Why MAC Matters:** Even if an attacker compromises a user account, MAC policies prevent privilege escalation and limit damage by restricting what the compromised process can access.

### Best Practices Implemented

✅ Defense in depth with multiple security layers  
✅ Automated monitoring and alerting  
✅ Regular security updates  
✅ Comprehensive logging and audit trails  
✅ Documented and repeatable security configurations  
✅ Remote monitoring capabilities  
✅ Intrusion detection and prevention  

---

**Lab Completion Date:** December 18, 2025  
**Total Implementation Time:** 4 hours  
**Systems Secured:** 1 Ubuntu server  
**Scripts Created:** 3 (apparmor-report.sh, security-baseline.sh, monitor-server.sh)
