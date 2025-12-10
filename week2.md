# Week 2: Security Planning and Testing Methodology

## Overview
This week focuses on designing a security baseline and establishing a performance testing methodology for the Linux server system.

---

## 1. Performance Testing Plan

### Remote Monitoring Methodology

**Approach:**
- Describe how you'll monitor the server remotely from your workstation via SSH
- Explain which commands and tools you'll use (e.g., `top`, `htop`, `vmstat`, `iostat`, `netstat`)
- Detail how you'll collect and store performance data

**Testing Approach:**
- **Baseline Testing:** Measure system performance at idle state
- **Load Testing:** Apply various workloads to stress different components
- **Comparison:** Compare results across different scenarios
- **Documentation:** How you'll record and present findings

**Example Structure:**
```
Monitoring will be conducted via SSH from the workstation using:
- CPU monitoring: top, mpstat
- Memory monitoring: free -h, vmstat
- Disk I/O: iostat, df -h
- Network: ss, netstat, iftop
- Custom script: monitor-server.sh (to be developed in Week 5)
```

---

## 2. Security Configuration Checklist

### SSH Hardening
- [ ] Configure SSH key-based authentication
- [ ] Disable password authentication
- [ ] Disable root login
- [ ] Change default SSH port (optional but recommended)
- [ ] Configure SSH idle timeout
- [ ] Limit SSH access to specific users/groups

### Firewall Configuration
- [ ] Install and enable firewall (ufw/iptables)
- [ ] Default deny incoming traffic
- [ ] Allow SSH from workstation IP only
- [ ] Allow required application ports
- [ ] Block all other connections
- [ ] Document all rules

### Mandatory Access Control
- [ ] Choose SELinux or AppArmor (depending on distribution)
- [ ] Enable MAC system
- [ ] Configure policies for critical services
- [ ] Document enforcement mode
- [ ] Plan monitoring approach

### Automatic Updates
- [ ] Configure unattended-upgrades (Ubuntu/Debian) or dnf-automatic (Fedora/RHEL)
- [ ] Set update frequency
- [ ] Configure which packages to auto-update
- [ ] Set up update notifications
- [ ] Plan reboot strategy for kernel updates

### User Privilege Management
- [ ] Create non-root administrative user
- [ ] Configure sudo access
- [ ] Implement principle of least privilege
- [ ] Remove unnecessary user accounts
- [ ] Set strong password policies (if passwords used)

### Network Security
- [ ] Configure host-based firewall
- [ ] Disable unnecessary network services
- [ ] Plan fail2ban implementation (Week 5)
- [ ] Document network topology
- [ ] Plan intrusion detection approach

---

## 3. Threat Model

### Threat 1: Brute Force SSH Attacks

**Description:**  
Attackers attempt to gain unauthorized access by repeatedly trying username/password combinations on the SSH service.

**Risk Level:** High

**Mitigation Strategies:**
- Implement key-based authentication only (disable passwords)
- Configure fail2ban to ban IPs after failed login attempts
- Restrict SSH access to specific IP addresses via firewall
- Use non-standard SSH port (security through obscurity)
- Monitor auth logs for suspicious activity

---

### Threat 2: Privilege Escalation

**Description:**  
An attacker with limited access exploits vulnerabilities to gain root or administrative privileges.

**Risk Level:** Medium-High

**Mitigation Strategies:**
- Apply automatic security updates to patch known vulnerabilities
- Configure sudo with minimal necessary permissions
- Implement mandatory access control (SELinux/AppArmor)
- Regular security audits using Lynis
- Monitor sudo logs for unauthorized attempts

---

### Threat 3: Unauthorized Network Access

**Description:**  
Attackers attempt to access services or scan for vulnerabilities through unprotected network ports.

**Risk Level:** Medium

**Mitigation Strategies:**
- Configure restrictive firewall rules (default deny)
- Only allow SSH from specific workstation IP
- Disable and remove unnecessary network services
- Regular port scanning with nmap to verify configuration
- Implement intrusion detection with fail2ban

---

## 4. Reflections and Learning

### Key Considerations:
- What security principles are most important for a headless server?
- How do security measures impact system performance?
- What trade-offs exist between security and usability?
- Which threats pose the greatest risk to this specific deployment?

### Questions to Explore:
- How will I verify that security controls are working?
- What monitoring is needed to detect security incidents?
- How will performance testing interact with security controls?

### Next Steps:
- Finalize application selection for performance testing (Week 3)
- Prepare for initial system configuration (Week 4)
- Document any additional security considerations identified

---

## 5. Command Line Evidence

### Screenshot 1: SSH Configuration Review
![SSH Config Screenshot](images/week2_ssh_config.png)
*Caption: Reviewing current SSH configuration settings*

**Command:**
```bash
cat /etc/ssh/sshd_config
```

**Purpose:**  
[Explain what you're checking and why]

---

### Screenshot 2: Firewall Status Check
![Firewall Status Screenshot](images/week2_firewall_status.png)
*Caption: Current firewall configuration*

**Command:**
```bash
sudo ufw status verbose
```

**Purpose:**  
[Explain what you're verifying]

---

### Screenshot 3: Security Updates Available
![Security Updates Screenshot](images/week2_security_updates.png)
*Caption: Checking for available security patches*

**Command:**
```bash
apt list --upgradable | grep security
```

**Purpose:**  
[Explain why you're checking for updates]

---

### Screenshot 4: System Information
![System Info Screenshot](images/week2_system_info.png)
*Caption: Documenting server system specifications*

**Commands:**
```bash
uname -a
lsb_release -a
free -h
df -h
```

**Purpose:**  
[Explain what system information you're gathering]

---

### Screenshot 5: Network Configuration
![Network Config Screenshot](images/week2_network_config.png)
*Caption: Server network settings and IP addressing*

**Command:**
```bash
ip addr show
ip route show
```

**Purpose:**  
[Explain network configuration you're documenting]

---

## References

[1] "
