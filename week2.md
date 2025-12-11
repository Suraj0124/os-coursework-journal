# Week 2: Security Planning and Testing Methodology

## Overview
This week focused on planning the security baseline and performance testing methodology before implementation begins in Week 4.
---

## 1. Performance Testing Plan

### Remote Monitoring Methodology

#### Approach
All performance monitoring will be conducted remotely from the workstation via SSH. This ensures:
- No GUI overhead on the server
- Professional remote administration practice
- Realistic cloud infrastructure simulation

#### Testing Phases
1. **Baseline Testing** - Measure system performance at idle
2. **Application Load Testing** - Run applications and measure resource consumption
3. **Performance Analysis** - Identify bottlenecks using monitoring data
4. **Optimization Testing** - Implement improvements and re-measure

### Monitoring Tools and Commands

| Tool | Purpose | Usage |
|------|---------|-------|
| `top` / `htop` | Real-time process monitoring | CPU, memory, process stats |
| `vmstat` | Virtual memory statistics | Memory, swap, I/O |
| `iostat` | Disk I/O performance | Read/write speeds |
| `netstat` / `ss` | Network connections | Active connections, ports |
| `sar` | System activity reporting | Historical performance data |
| Custom scripts | Automated monitoring | Week 5 implementation |

### Metrics to Collect

For each application tested:
- **CPU Usage**: Percentage utilization
- **Memory Usage**: MB/GB used, percentage
- **Disk I/O**: Reads/writes per second
- **Network Performance**: Throughput (Mbps), latency (ms)
- **Response Times**: Application-specific metrics
- **System Load**: 1, 5, 15-minute averages

### Data Collection Strategy

```bash
# Remote monitoring approach (to be implemented Week 5-6)
# From workstation, SSH into server and collect:
ssh user@server 'top -bn1 | head -20'
ssh user@server 'free -h'
ssh user@server 'df -h'
ssh user@server 'iostat -x 1 5'
```

---

## 2. Security Configuration Checklist

### SSH Hardening (Implementation: Week 4)
- [ ] Generate SSH key pair on workstation
- [ ] Copy public key to server
- [ ] Configure key-based authentication
- [ ] Disable password authentication (`PasswordAuthentication no`)
- [ ] Disable root login (`PermitRootLogin no`)
- [ ] Change default SSH port (optional security through obscurity)
- [ ] Set SSH timeout values (`ClientAliveInterval`, `ClientAliveCountMax`)
- [ ] Restrict SSH access to specific users (`AllowUsers`)

**Configuration File**: `/etc/ssh/sshd_config`

---

### Firewall Configuration (Implementation: Week 4)
- [ ] Install and enable UFW (Uncomplicated Firewall)
- [ ] Set default deny incoming policy
- [ ] Set default allow outgoing policy
- [ ] Allow SSH from workstation IP only
- [ ] Deny SSH from all other sources
- [ ] Document complete firewall ruleset
- [ ] Test connectivity from workstation
- [ ] Test blocked access from other sources

**Commands**:
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from WORKSTATION_IP to any port 22
sudo ufw enable
```

---

### Mandatory Access Control (Implementation: Week 5)
- [ ] Choose between SELinux or AppArmor
- [ ] Enable MAC system
- [ ] Configure profiles for critical services
- [ ] Test enforcement mode
- [ ] Document policy configurations
- [ ] Monitor access control logs

**Choice**: [SELinux / AppArmor] - **Justification**: [To be documented]

---

### Automatic Security Updates (Implementation: Week 5)
- [ ] Install unattended-upgrades package
- [ ] Configure automatic security updates
- [ ] Set update schedule
- [ ] Configure email notifications (optional)
- [ ] Test update mechanism
- [ ] Monitor update logs

**Configuration File**: `/etc/apt/apt.conf.d/50unattended-upgrades`

---

### User Privilege Management (Implementation: Week 4)
- [ ] Create non-root administrative user
- [ ] Configure sudo access
- [ ] Disable direct root login
- [ ] Implement principle of least privilege
- [ ] Document user roles and permissions
- [ ] Test sudo functionality

**Users**:
- Administrative user: [username]
- Regular users: [if any]

---

### Network Security (Implementation: Week 5-7)
- [ ] Configure fail2ban for intrusion detection
- [ ] Disable unused network services
- [ ] Review listening ports
- [ ] Implement port knocking (optional)
- [ ] Configure TCP wrappers (optional)
- [ ] Document all allowed services

---

## 3. Threat Model

### Threat 1: Brute Force SSH Attacks

**Description**: Attackers attempt to gain access by systematically trying username/password combinations against the SSH service.

**Likelihood**: High (SSH is commonly targeted)  
**Impact**: High (full system compromise if successful)

**Mitigation Strategies**:
1. Implement key-based authentication (removes password attack surface)
2. Disable password authentication completely
3. Restrict SSH access to specific IP (workstation only)
4. Deploy fail2ban to automatically block repeated failed attempts
5. Consider changing default SSH port (security through obscurity)

**Implementation Weeks**: 4, 5

---

### Threat 2: Privilege Escalation

**Description**: A compromised non-privileged user account exploits vulnerabilities to gain root/administrator privileges.

**Likelihood**: Medium (requires initial compromise)  
**Impact**: Critical (complete system control)

**Mitigation Strategies**:
1. Apply principle of least privilege for all users
2. Configure sudo with specific command restrictions
3. Keep system packages updated (automatic updates)
4. Enable and configure mandatory access control (SELinux/AppArmor)
5. Regular security audits with Lynis
6. Monitor authentication logs for suspicious activity

**Implementation Weeks**: 4, 5, 7

---

### Threat 3: Unpatched Vulnerabilities

**Description**: Outdated software packages contain known security vulnerabilities that attackers can exploit.

**Likelihood**: Medium (depends on update frequency)  
**Impact**: High (varies by vulnerability)

**Mitigation Strategies**:
1. Enable automatic security updates
2. Configure unattended-upgrades for daily security patches
3. Regular manual system updates (`sudo apt update && sudo apt upgrade`)
4. Monitor security mailing lists for critical vulnerabilities
5. Implement security scanning with Lynis (Week 7)
6. Maintain minimal installed packages (reduce attack surface)

**Implementation Weeks**: 5, ongoing

---

### Threat 4: Network-Based Attacks (Additional Threat)

**Description**: Attacks originating from the network, including port scanning, service exploitation, and denial of service.

**Likelihood**: Medium (exposed network services)  
**Impact**: Medium to High (service disruption or compromise)

**Mitigation Strategies**:
1. Firewall configuration with restrictive rules
2. Disable unnecessary network services
3. Regular port scanning audits (nmap)
4. fail2ban for automated response to attacks
5. Network monitoring for unusual traffic patterns

**Implementation Weeks**: 4, 5, 7

---

## 4. Current System Baseline (Optional)

### Pre-Hardening Security State

**Note**: These screenshots document the system state BEFORE security implementation begins in Week 4.

#### System Information
```bash
# Command: uname -a
[Screenshot showing system information]

# Command: lsb_release -a
[Screenshot showing Ubuntu version]
```

#### Current Firewall Status
```bash
# Command: sudo ufw status
[Screenshot - likely showing "inactive" or default state]
```

#### SSH Configuration (Default)
```bash
# Command: cat /etc/ssh/sshd_config | grep -v "^#" | grep -v "^$"
[Screenshot showing default SSH settings]
```

#### Running Services
```bash
# Command: sudo systemctl list-units --type=service --state=running
[Screenshot showing active services]
```

#### Network Listening Ports
```bash
# Command: sudo netstat -tulpn | grep LISTEN
# Or: sudo ss -tulpn | grep LISTEN
[Screenshot showing open ports]
```

---

## 5. Testing Methodology Summary

### Week-by-Week Implementation Plan

| Week | Focus Area | Deliverables |
|------|-----------|--------------|
| 2 | Planning (current) | Testing plan, security checklist, threat model |
| 3 | Application selection | Choose applications for performance testing |
| 4 | Basic security | SSH hardening, firewall, user management |
| 5 | Advanced security | MAC, fail2ban, monitoring scripts |
| 6 | Performance testing | Execute tests, collect data, optimize |
| 7 | Security audit | Lynis scan, nmap, final evaluation |

---

## 6. Reflection

### Learning Objectives for This Week
- Understanding security planning before implementation
- Identifying potential threats and mitigation strategies
- Planning comprehensive performance testing methodology
- Establishing baseline for future comparison

### Challenges Anticipated
1. **Balancing security and usability**: Ensuring hardened configuration doesn't impede legitimate administration
2. **Performance overhead**: Understanding the cost of security controls
3. **Remote monitoring**: Developing effective SSH-based monitoring approach

### Next Steps (Week 3)
- Select applications for performance testing
- Research resource profiles for chosen applications
- Plan installation methodology via SSH

---

## References

[1] "SSH Hardening Guide," Ubuntu Documentation. [Online]. Available: https://help.ubuntu.com/community/SSH/OpenSSH/Configuring [Accessed: Date]

[2] "UFW - Uncomplicated Firewall," Ubuntu. [Online]. Available: https://help.ubuntu.com/community/UFW [Accessed: Date]

[3] "OWASP Top Ten," OWASP Foundation. [Online]. Available: https://owasp.org/www-project-top-ten/ [Accessed: Date]



