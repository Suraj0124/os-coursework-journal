# Week 2: Security Planning and Testing Methodology

---

## 1. Performance Testing Plan

### Objective
To establish a comprehensive remote monitoring methodology for evaluating Linux server performance under various workload conditions without installing resource-intensive GUI applications.

### Remote Monitoring Methodology

#### Architecture Approach
I have adopted an **agentless monitoring strategy** that leverages standard Linux CLI utilities accessed exclusively via SSH from the workstation. This approach ensures:
- Minimal resource overhead on the server
- No additional software dependencies
- Industry-standard remote administration practices
- Real-time performance visibility

#### Monitoring Tool Selection

| Tool | Purpose | Metrics Collected | Update Frequency |
|------|---------|-------------------|------------------|
| `top` / `htop` / `btop` | Process monitoring | CPU %, Memory %, Load average, Process count | Real-time (1-2 sec) |
| `free` | Memory analysis | Total, Used, Free, Available RAM | On-demand |
| `vmstat` | Virtual memory stats | CPU, Memory, Swap, I/O, System | 1-5 second intervals |
| `iostat` | Disk I/O performance | Read/Write throughput, IOPS, Utilization | 2-5 second intervals |
| `mpstat` | CPU per-core analysis | Individual core utilization | 1-5 second intervals |
| `ss` | Network connections | Active connections, Listening ports | On-demand |
| `sar` | System activity | Historical trends, Network bandwidth | 1-5 second intervals |

**Screenshot: System Idle State Baseline**

![System Idle](images/week2_idle_state.png)
*Caption: Server baseline resource consumption using htop*

**Screenshot: Process Hierarchy**

![Process Tree](images/week2_pstree.png)
*Caption: Process tree showing system service relationships*

---

### Testing Approach

#### Phase 1: Baseline Establishment
Capture idle system metrics to establish performance baseline.

**Baseline Collection Commands:**
```bash
free -h                    # Memory baseline
df -h                      # Disk space baseline
mpstat -P ALL 1 3          # CPU baseline per core
iostat -x 2 3              # Disk I/O baseline
vmstat 1 5                 # Virtual memory baseline
ss -s                      # Network socket baseline
```

**Screenshot: Baseline Metrics**

![Baseline](images/week2_baseline.png)
*Caption: System baseline showing idle resource consumption*

---

#### Phase 2: Load Generation
Generate controlled system stress using load testing tools.

**Load Testing Tools:**
```bash
sudo apt install stress sysbench
```

**Test Scenarios:**
```bash
# CPU stress
stress --cpu 4 --timeout 60&

# Memory stress
stress --vm 2 --vm-bytes 1G --timeout 60&

# Combined stress
stress --cpu 2 --vm 1 --vm-bytes 512M --io 2 --timeout 60&
```

**Screenshot: Stress Test Execution**

![Stress Command](images/week2_stress_cmd.png)
*Caption: Initiating CPU stress test*

---

#### Phase 3: Monitoring Under Load
Observe system behavior during stress testing.

**Screenshot: System Under Load**

![Under Load](images/week2_under_load.png)
*Caption: Resource utilization during stress test*

---

#### Phase 4: Comparison Analysis
Compare baseline vs. load metrics to identify performance characteristics and bottlenecks.

---

## 2. Security Configuration Checklist

### SSH Hardening

#### ☐ Configure SSH Key-Based Authentication

**Task:** Generate and deploy Ed25519 SSH keys

**Commands:**
```bash
# On Workstation: Generate key
ssh-keygen -t ed25519 -C "server-key"

# On Workstation: Deploy to server
ssh-copy-id -i ~/.ssh/id_ed25519.pub username@server_ip
```

**Why Ed25519?**
- 256-bit key ≈ 3072-4096 bit RSA strength
- Faster performance, lower CPU usage
- Resistant to timing and cache attacks
- Modern cryptography standard

**Screenshot: SSH Key Generation**

![SSH Keygen](images/week2_keygen.png)
*Caption: Generating Ed25519 SSH key pair on workstation*

**Screenshot: Public Key Deployment**

![SSH Copy ID](images/week2_ssh_copy.png)
*Caption: Copying public key to server*

---

#### ☐ Disable Password Authentication

**Task:** Edit `/etc/ssh/sshd_config` to disable password-based login

**Configuration Change:**
```bash
PasswordAuthentication no
```

**Security Benefit:** Eliminates brute-force password attacks entirely

---

#### ☐ Disable Root Login

**Task:** Prevent direct root SSH access

**Configuration Change:**
```bash
PermitRootLogin no
```

**Security Benefit:** Forces attackers to know a specific username, adds authentication layer

---

#### ☐ Change Default SSH Port (Optional)

**Task:** Modify SSH port from 22 to reduce automated scans

**Configuration Change:**
```bash
Port 2222
```

**Security Benefit:** Reduces noise from automated bots scanning port 22

---

#### ☐ Configure SSH Idle Timeout

**Task:** Disconnect idle SSH sessions automatically

**Configuration Change:**
```bash
ClientAliveInterval 300
ClientAliveCountMax 2
```

**Security Benefit:** Prevents session hijacking of idle connections (10 min timeout)

---

#### ☐ Limit SSH Access to Specific Users

**Task:** Whitelist authorized users

**Configuration Change:**
```bash
AllowUsers adminuser
```

**Security Benefit:** Explicit access control, reduces unauthorized access attempts

---

**Screenshot: SSH Config Before**

![SSHD Before](images/week2_sshd_before.png)
*Caption: Original SSH configuration before hardening*

**Screenshot: SSH Config After**

![SSHD After](images/week2_sshd_after.png)
*Caption: Hardened SSH configuration with security enhancements*

---

### Firewall Configuration

#### ☐ Install and Enable Firewall

**Task:** Install UFW firewall

**Commands:**
```bash
sudo apt install ufw
```

---

#### ☐ Configure Default Deny Policy

**Task:** Block all incoming traffic by default

**Commands:**
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

**Security Principle:** Default-deny approach (principle of least privilege)

---

#### ☐ Allow SSH from Workstation Only

**Task:** Permit SSH connections only from specific workstation IP

**Command:**
```bash
sudo ufw allow from 192.168.56.103 to any port 2222 proto tcp
```

**Security Benefit:** Network-level access control prevents unauthorized connection attempts

---

#### ☐ Enable and Verify Firewall

**Commands:**
```bash
sudo ufw enable
sudo ufw status verbose
```

**Screenshot: Firewall Configuration**

![UFW Setup](images/week2_ufw_setup.png)
*Caption: Configuring firewall with restrictive rules*

**Screenshot: Firewall Status**

![UFW Status](images/week2_ufw_status.png)
*Caption: Active firewall showing SSH restricted to workstation IP*

---

### Mandatory Access Control

#### ☐ Verify AppArmor is Active

**Task:** Confirm MAC system is enforcing profiles

**Command:**
```bash
sudo aa-status
```

**Screenshot: AppArmor Status**

![AppArmor](images/week2_apparmor.png)
*Caption: AppArmor profiles loaded and enforced*

**What is AppArmor?**
- Mandatory Access Control (MAC) system
- Confines programs to limited resources
- Prevents compromised applications from unauthorized access
- Default in Ubuntu

---

#### ☐ Review Active Profiles

**Task:** Document which services have AppArmor protection

**Expected Profiles:**
- `/usr/sbin/sshd`
- `/usr/sbin/tcpdump`
- System utilities

---

#### ☐ Plan Profile Configuration (Week 5)

**Future Tasks:**
- Track access control settings
- Report on enforcement status
- Configure custom profiles if needed

---

### Automatic Security Updates

#### ☐ Install Unattended-Upgrades Package

**Task:** Install automatic update package

**Command:**
```bash
sudo apt install unattended-upgrades
```

**Screenshot: Installation**

![Unattended Install](images/week2_unattended_install.png)
*Caption: Installing automatic security update package*

---

#### ☐ Enable Automatic Updates

**Task:** Configure system to automatically install security patches

**Command:**
```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

**Screenshot: Configuration**

![Unattended Config](images/week2_unattended_config.png)
*Caption: Enabling automatic security updates*

---

#### ☐ Verify Service is Running

**Command:**
```bash
sudo systemctl status unattended-upgrades
```

**Screenshot: Service Status**

![Unattended Status](images/week2_unattended_status.png)
*Caption: Verifying automatic update service is active*

---

#### ☐ Configure Update Policy

**Task:** Define which updates to install automatically

**Configuration File:** `/etc/apt/apt.conf.d/50unattended-upgrades`

**Recommended Settings:**
- Security updates: Yes (automatic)
- Stable updates: Optional
- Automatic reboot: Consider for non-critical systems

---

### User Privilege Management

#### ☐ Create Non-Root Administrative User

**Task:** Create dedicated admin user account

**Command:**
```bash
sudo adduser adminuser
```

**Screenshot: User Creation**

![User Creation](images/week2_user_create.png)
*Caption: Creating non-root administrative user*

---

#### ☐ Grant Sudo Privileges

**Task:** Add user to sudo group

**Command:**
```bash
sudo usermod -aG sudo adminuser
```

**Security Principle:** Separate root privileges from regular user access

---

#### ☐ Verify Group Membership

**Commands:**
```bash
groups adminuser
id adminuser
```

**Screenshot: Group Verification**

![Groups](images/week2_groups.png)
*Caption: Confirming adminuser is in sudo group*

---

#### ☐ Test Sudo Access

**Commands:**
```bash
su - adminuser
sudo whoami
```

**Screenshot: Sudo Test**

![Sudo Test](images/week2_sudo_test.png)
*Caption: Verifying sudo privileges work correctly*

---

#### ☐ List All Sudo Users

**Command:**
```bash
getent group sudo
```

**Screenshot: Sudo Users**

![Sudo Users](images/week2_sudo_users.png)
*Caption: All users with sudo privileges*

---

#### ☐ Implement Principle of Least Privilege

**Task:** Configure sudo with minimal necessary permissions

**Future Configuration (Week 5):**
- Password timeout settings
- Command logging
- Restricted sudo commands if needed

---

### Network Security

#### ☐ Document Network Topology

**Task:** Map network connections between workstation and server

**Components:**
- VirtualBox network type (NAT/Bridged/Host-only)
- IP addressing scheme
- Network isolation approach

---

#### ☐ Identify Listening Services

**Command:**
```bash
sudo ss -tlnp
```

**Screenshot: Listening Ports**

![SS Listening](images/week2_ss_listening.png)
*Caption: Services listening on network ports before hardening*

---

#### ☐ Document Network Configuration

**Commands:**
```bash
ip addr show
ip route show
```

**Screenshot: Network Config**

![IP Config](images/week2_ip_config.png)
*Caption: Server IP addressing and routing*

---

#### ☐ Plan Intrusion Detection (Week 5)

**Future Implementation:**
- Install and configure fail2ban
- Define ban policies
- Configure email alerts
- Monitor authentication logs

---

## 3. Threat Model

### Threat 1: Brute Force SSH Attacks

**Description:**  
Attackers use automated tools (Hydra, Medusa, John the Ripper) to repeatedly attempt SSH login using common passwords and username combinations from dictionaries like rockyou.txt.

**Attack Vector:**
- Automated bots continuously scan for SSH (port 22)
- Dictionary attacks using leaked password databases
- Credential stuffing from data breaches
- Username enumeration through timing attacks

**Risk Level:** High

**Potential Impact:**
- Unauthorized system access
- Data theft or destruction
- Installation of backdoors/rootkits
- Lateral movement to other systems
- Use of compromised server for botnet activities

**Mitigation Strategies Implemented:**

✅ **Disabled Password Authentication**
- Configuration: `PasswordAuthentication no`
- Effect: Completely eliminates password-based login
- Attack Prevention: Makes dictionary attacks impossible

✅ **Enforced Public Key Authentication**
- Configuration: `PubkeyAuthentication yes`
- Effect: Requires possession of private key
- Attack Prevention: Cryptographically impossible to brute force

✅ **Changed Default SSH Port**
- Configuration: `Port 2222`
- Effect: Reduces automated scan effectiveness
- Attack Prevention: Most bots only scan port 22

✅ **Restricted SSH to Workstation IP**
- Configuration: UFW rule allowing only 192.168.56.103
- Effect: Network-level access control
- Attack Prevention: Blocks all connection attempts except from authorized IP

✅ **Disabled Root Login**
- Configuration: `PermitRootLogin no`
- Effect: Forces username enumeration first
- Attack Prevention: Attackers must know valid username

**Planned Additional Mitigations (Week 5):**
- 🔜 Implement fail2ban for dynamic IP blocking
- 🔜 Configure SSH login alerts
- 🔜 Monitor authentication logs for suspicious activity

**Residual Risk:** Low  
Multiple overlapping controls make successful SSH attack highly unlikely.

---

### Threat 2: Unauthorized Network Access and Service Exploitation

**Description:**  
Attackers perform network reconnaissance using tools like nmap, masscan, or Shodan to identify open ports and running services, then exploit known vulnerabilities (CVEs) in those services.

**Attack Vector:**
- Port scanning to identify open services
- Service version fingerprinting
- Exploitation of unpatched vulnerabilities
- Zero-day attacks against exposed services
- Denial of Service (DoS) attacks

**Risk Level:** Medium-High

**Potential Impact:**
- Remote code execution on server
- Service disruption (DoS/DDoS)
- Information disclosure
- Privilege escalation vectors
- Installation of malware

**Mitigation Strategies Implemented:**

✅ **Default Deny Firewall Policy**
- Configuration: `ufw default deny incoming`
- Effect: All incoming traffic blocked by default
- Attack Prevention: Only explicitly allowed services accessible

✅ **Minimal Service Footprint**
- Configuration: Headless server, no GUI
- Effect: Fewer services running = smaller attack surface
- Attack Prevention: Reduces potential vulnerability count

✅ **Automatic Security Patching**
- Configuration: unattended-upgrades enabled
- Effect: Vulnerabilities patched automatically
- Attack Prevention: Reduces window of vulnerability exploitation

✅ **Network Segmentation**
- Configuration: Isolated VirtualBox network
- Effect: No direct internet exposure
- Attack Prevention: Limits attacker reach

✅ **SSH-Only Access**
- Configuration: Only SSH service exposed to network
- Effect: Single point of network entry
- Attack Prevention: Concentrated security focus

**Planned Additional Mitigations (Week 7):**
- 🔜 Port scan verification with nmap
- 🔜 Service audit and justification
- 🔜 Lynis security assessment
- 🔜 Disable unnecessary services

**Residual Risk:** Medium  
Risk depends on applications selected in Week 3. Will reassess after application deployment.

---

### Threat 3: Privilege Escalation

**Description:**  
An attacker who has gained limited user access exploits system misconfigurations or vulnerabilities to elevate privileges to root, gaining complete system control.

**Attack Vector:**
- Exploitation of sudo misconfigurations
- SUID/SGID binary vulnerabilities
- Kernel exploits (local privilege escalation CVEs)
- Weak file permissions on sensitive files
- Path hijacking attacks
- Exploiting running services with elevated privileges

**Risk Level:** Medium-High

**Potential Impact:**
- Complete system compromise
- Bypass of all security controls
- Installation of rootkits
- Persistent backdoor access
- Data theft and manipulation
- Lateral movement to other systems

**Mitigation Strategies Implemented:**

✅ **Disabled Root SSH Login**
- Configuration: `PermitRootLogin no`
- Effect: Cannot directly access root account
- Attack Prevention: Must compromise user account first, then escalate

✅ **Created Limited Administrative User**
- Configuration: adminuser with sudo group membership
- Effect: Sudo requires password re-authentication
- Attack Prevention: Adds authentication barrier

✅ **Principle of Least Privilege**
- Configuration: Non-root user for daily operations
- Effect: Limited initial access if compromised
- Attack Prevention: Attacker starts with restricted permissions

✅ **Mandatory Access Control (AppArmor)**
- Configuration: AppArmor profiles active
- Effect: Confines applications to defined resources
- Attack Prevention: Limits damage from compromised processes

✅ **Automatic Kernel Updates**
- Configuration: Security updates include kernel patches
- Effect: Known privilege escalation vulnerabilities patched
- Attack Prevention: Reduces exploitable CVEs

**Planned Additional Mitigations (Week 5):**
- 🔜 Enhanced sudo logging
- 🔜 Audit SUID/SGID binaries
- 🔜 File permission review
- 🔜 Custom AppArmor profiles

**Planned Additional Mitigations (Week 7):**
- 🔜 Lynis privilege escalation checks
- 🔜 Kernel hardening review
- 🔜 System configuration audit

**Residual Risk:** Medium  
Multiple layers force attackers to chain exploits. Ongoing patching reduces vulnerability window.

---

## 4. System Documentation

### System Specifications

**Commands:**
```bash
uname -a
lsb_release -a
```

**Screenshot: System Information**

![System Info](images/week2_system_info.png)
*Caption: Kernel version, distribution details*

---

### Resource Availability

**Commands:**
```bash
free -h
df -h
```

**Screenshot: Resources**

![Resources](images/week2_resources.png)
*Caption: Available memory and disk space*

---

## 5. Reflections and Learning

### Key Takeaways

**Security Architecture:**
- Defense in depth requires multiple overlapping controls
- Default-deny policies are more secure than default-allow
- Key-based authentication is vastly superior to passwords
- Network-level controls (firewall) complement application-level security

**Performance Monitoring:**
- Agentless monitoring minimizes server overhead
- Baseline establishment is critical for comparison
- Different tools provide different perspectives on system behavior
- Remote monitoring via SSH is industry-standard practice

**Trade-offs Identified:**
- **Security vs. Usability:** Disabling password auth improves security but requires careful key management
- **Port Changes vs. Security:** Changing SSH port reduces noise but provides only obscurity, not real security
- **Automatic Updates vs. Stability:** Auto-updates patch vulnerabilities but may introduce compatibility issues

### Challenges Anticipated

**Technical Challenges:**
- Ensuring firewall rules don't lock out legitimate access
- Managing SSH keys across multiple systems
- Balancing automatic updates with system stability
- Understanding AppArmor profile syntax

**Planning Challenges:**
- Selecting appropriate applications for performance testing (Week 3)
- Designing realistic load test scenarios
- Determining acceptable performance baselines
- Identifying relevant security metrics

### Questions for Further Exploration

1. How do I verify that security controls are actually working as intended?
2. What is the performance overhead of AppArmor profiles?
3. How should I respond if fail2ban blocks legitimate access attempts?
4. What are the signs of an ongoing SSH attack in the logs?
5. How do I balance security hardening with troubleshooting accessibility?



## References

[1] "OpenSSH Manual Pages," OpenSSH, [Online]. Available: https://www.openssh.com/manual.html  
[2] "UFW - Uncomplicated Firewall," Ubuntu Documentation, [Online]. Available: https://help.ubuntu.com/community/UFW  
[3] "AppArmor Wiki," Ubuntu, [Online]. Available: https://wiki.ubuntu.com/AppArmor  
[4] "Ed25519: High-speed high-security signatures," cr.yp.to, [Online]. Available: https://ed25519.cr.yp.to/  
[5] "UnattendedUpgrades," Debian Wiki, [Online]. Available: https://wiki.debian.org/UnattendedUpgrades  
[6] "Linux System Monitoring Tools," Red Hat Documentation, [Online]. Available: https://access.redhat.com/documentation/

-
