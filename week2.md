# Week 2: Security Planning and Testing Methodology

## 1. Performance Testing Plan

### Remote Monitoring Approach
Agentless monitoring via SSH using standard CLI tools - no GUI applications on headless server.

### Tools Selected

| Tool | Purpose | Key Command | Use Case |
|------|---------|-------------|----------|
| **top** | Quick process monitoring | `top` then `P` (CPU) or `M` (memory) | Rapid system checks |
| **htop** | Interactive monitoring | `htop` | Detailed process analysis |
| **btop** | Modern comprehensive view | `btop` | Full system overview |
| **ps aux** | Process snapshot | `ps aux --sort=-%cpu` | Scripting and automation |
| **nmon** | Data export | `nmon -a -o output.csv` | Performance data collection |

**Installation:**
```bash
sudo apt install htop btop nmon
```

### Testing Methodology

**Phase 1: Baseline (Idle State)**
- 5-minute idle measurement
- Document CPU, memory, disk, network baseline

**Screenshots:**
![System idle - top](images/week2/top-idle.png)
![System idle - htop](images/week2/htop-idle.png)
![System idle - btop](images/week2/btop-idle.png)

---

**Phase 2: Stress Testing**

Tool: `stress` (lightweight load generator)

```bash
sudo apt install stress
stress --cpu 4 --timeout 60&
```

**Results:**
![top during stress](images/week2/top-stress.png)
![btop during stress](images/week2/btop-stress.png)
![nmon during stress](images/week2/nmon-stress.png)
![Process tree](images/week2/pstree-stress.png)

**Observations:**
- All monitoring tools successfully captured resource spikes
- SSH remained stable during CPU stress
- System recovered immediately after test
- Ready for Week 6 performance evaluation

---

## 2. Security Implementation

### SSH Hardening

#### Key Generation: Ed25519

```bash
ssh-keygen -t ed25519 -C "server-key"
ssh-copy-id adminuser@192.168.10.10
```

**Why Ed25519 over RSA:**
- Stronger: 256-bit = 3072-4096 bit RSA equivalent
- Faster: Much better performance for key generation and authentication
- Safer: Resistant to timing attacks and implementation errors

![SSH key generation](images/week2/ssh-keygen.png)

---

#### SSH Configuration Changes

**Backup first:**
```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
sudo nano /etc/ssh/sshd_config
```

**Changes made:**
```bash
PasswordAuthentication no       # Eliminates brute-force attacks
PubkeyAuthentication yes        # Requires cryptographic key
PermitRootLogin no              # Prevents direct root access
Port 2424                       # Reduces automated scans
```

**Restart SSH:**
```bash
sudo systemctl restart sshd
```

![Before configuration](images/week2/ssh-before.png)
![After configuration](images/week2/ssh-after.png)

---

### Firewall Configuration

```bash
# Set default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH from workstation only
sudo ufw allow from 192.168.10.3 to any port 2424

# Enable firewall
sudo ufw enable
sudo ufw status verbose
```

![Firewall rules](images/week2/ufw-rules.png)

**Security Impact:** SSH access restricted to single trusted IP

---

### User and Privilege Management

```bash
# Create administrative user
sudo adduser adminuser
sudo usermod -aG sudo adminuser

# Verify
groups adminuser
sudo -l -U adminuser
```

![User creation](images/week2/adduser.png)
![Sudo verification](images/week2/sudo-test.png)

---

### AppArmor (Mandatory Access Control)

```bash
sudo aa-status
```

- Pre-installed on Ubuntu
- Confines applications to security profiles
- Will configure profiles in Week 5

![AppArmor status](images/week2/apparmor-status.png)

---

### Automatic Security Updates

```bash
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
sudo systemctl status unattended-upgrades
```

![Unattended-upgrades](images/week2/unattended-status.png)

**Rationale:** Reduces vulnerability window for known exploits

---

## 3. Threat Model

### Threat 1: Brute Force SSH Attacks

**Description:** Automated password guessing using tools like Hydra

**Likelihood:** High | **Impact:** Critical

**Mitigations:**
- ✅ Password authentication disabled → eliminates attack vector
- ✅ Key-based auth only → requires private key possession
- ✅ Firewall restricts to workstation IP → blocks remote attempts
- ✅ Custom port 2424 → reduces automated scans

**Risk Level:** Very Low after mitigations

---

### Threat 2: Network Reconnaissance

**Description:** Port scanning with nmap to discover vulnerabilities

**Likelihood:** High | **Impact:** Medium-High

**Mitigations:**
- ✅ UFW default deny → all ports filtered
- ✅ Single-IP whitelist → only workstation can connect
- ✅ Minimal services → only SSH running

**Risk Level:** Low after mitigations

---

### Threat 3: Privilege Escalation

**Description:** Compromised user attempts to gain root access

**Likelihood:** Medium | **Impact:** Critical

**Mitigations:**
- ✅ Root login disabled → can't directly access root
- ✅ Sudo required → creates audit trail
- ✅ AppArmor enabled → constrains application privileges
- ✅ Automatic updates → patches known vulnerabilities

**Risk Level:** Low-Medium after mitigations

---

## 4. Reflection

### Key Decisions

**1. Ed25519 over RSA**
- Stronger security with smaller keys
- Better performance for automation
- Modern standard

**2. Changed SSH Port to 2424**
- Reduces log noise from bots
- Acknowledged as "security through obscurity"
- Real security from keys + firewall

**3. Single-IP Firewall Whitelist**
- Strongest network control
- Zero-trust approach
- No usability impact for this architecture

**4. Automatic Security Updates**
- Industry best practice
- Reduces vulnerability window
- Low risk (security updates well-tested)

---

### Research Insights

**Defense in Depth:** Multiple independent security layers more effective than single strong control
- Network layer: Firewall
- Authentication: SSH keys
- Access control: No root login + sudo
- MAC: AppArmor
- Monitoring: Planned fail2ban (Week 5)

**Monitoring Overhead:** All tools <3% CPU impact, won't affect Week 6 performance tests

---

### Anticipated Challenges

| Challenge | Mitigation |
|-----------|-----------|
| **SSH key loss** | Backup keys securely; VirtualBox console access as emergency |
| **Firewall lockout** | Test rules before enabling; keep console access |
| **Config syntax errors** | Backup files; use `sshd -t` to test before applying |
| **Port confusion** | Create SSH config file with port settings |
| **Security vs testing** | Plan firewall rules for test apps; document adjustments |

---

### Next Steps (Week 3)

- Select diverse applications (CPU, RAM, I/O, network, server workloads)
- Plan firewall rules for test applications
- Verify monitoring compatibility with planned apps
- Document expected resource profiles

---

### Skills Developed

**Technical:** SSH hardening, firewall config, user management, remote monitoring  
**Analytical:** Threat modeling, risk assessment, trade-off analysis  
**Professional:** Technical documentation, decision justification

---

### References

[1] "OpenSSH Security Best Practices," Ubuntu Documentation, 2024.  
[2] D. J. Bernstein et al., "Ed25519: High-speed high-security signatures," 2012.  
[3] "SSH Key Management," Venafi Trust Protection Platform, 2024.

---

*Week 2 Completed: December 12, 2025*  
*Next: Week 3 - Application Selection*
