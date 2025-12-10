# Week 2: Security Planning and Testing Methodology

## Overview
This week establishes the foundation for secure server administration through comprehensive security planning and performance baseline testing.

---

## 1. Performance Testing Plan

### Objective
Design a methodology for monitoring system resources remotely and evaluating performance under different workloads.

### Remote Monitoring Methodology
I have implemented an agentless monitoring approach using standard CLI tools accessed via SSH, avoiding heavy GUI applications that consume server resources.

### Monitoring Tools

#### `top` / `htop` / `btop`
Interactive process viewers showing real-time CPU, memory, and process information.

**Installation & Usage:**
```bash
# top (pre-installed)
top

# htop (enhanced version)
sudo apt install htop
htop

# btop (modern visualization)
sudo apt install btop
btop
```

**Screenshot: System Idle State**

![System Idle](images/week2_idle_state.png)
*Caption: Baseline system resource usage with htop*

---

#### `ps aux` and `pstree`
Process listing and hierarchy visualization.

**Usage:**
```bash
ps aux --sort=-%mem    # Sort by memory
ps aux --sort=-%cpu    # Sort by CPU
pstree -p              # Process tree
```

**Screenshot: Process Tree**

![Process Tree](images/week2_pstree.png)
*Caption: System process hierarchy at baseline*

---

#### `vmstat` and `iostat`
System statistics including CPU, memory, disk I/O.

**Usage:**
```bash
vmstat 1 5             # Every 1 sec, 5 times
iostat -x 2 3          # Extended disk stats
```

**Screenshot: System Statistics**

![vmstat iostat](images/week2_vmstat_iostat.png)
*Caption: Virtual memory and I/O statistics*

---

#### `free` and `df`
Memory and disk usage information.

**Usage:**
```bash
free -h                # Human-readable memory
df -h                  # Disk space
```

**Screenshot: Memory and Disk**

![Memory Disk](images/week2_free_df.png)
*Caption: Available memory and disk space*

---

### Testing Approach

#### 1. Baseline Testing
Measure system at idle to establish baseline metrics.

#### 2. Load Generation
Use `stress` or `sysbench` to generate controlled system load.

**Installation:**
```bash
sudo apt install stress sysbench
```

**Example Load Test:**
```bash
stress --cpu 4 --timeout 60&
```

**Screenshot: Stress Command**

![Stress Command](images/week2_stress_cmd.png)
*Caption: Executing CPU stress test*

#### 3. Monitoring Under Load
Observe system behavior during stress testing.

**Screenshot: System Under Load**

![Under Load](images/week2_under_load.png)
*Caption: Resource utilization during stress test*

#### 4. Comparison Analysis
Compare baseline vs. load metrics to identify bottlenecks.

---

## 2. Security Configuration Checklist

### SSH Hardening

#### Generate SSH Keys

**Command:**
```bash
ssh-keygen -t ed25519 -C "server-key"
```

**Screenshot: Key Generation**

![SSH Keygen](images/week2_keygen.png)
*Caption: Generating Ed25519 SSH key pair*

**Why Ed25519?**
- 256-bit key ≈ 3072-4096 bit RSA strength
- Faster performance, lower CPU usage
- Resistant to timing and cache attacks
- Modern cryptography standard

---

#### Deploy Public Key

**Command:**
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server
```

**Screenshot: Key Deployment**

![SSH Copy ID](images/week2_ssh_copy.png)
*Caption: Copying public key to server*

---

#### SSH Configuration Hardening

**Backup original config:**
```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

**Screenshot: Before Configuration**

![SSHD Before](images/week2_sshd_before.png)
*Caption: Original SSH configuration*

**Key Changes:**
```bash
PasswordAuthentication no       # Disable passwords
PubkeyAuthentication yes        # Enable key auth
PermitRootLogin no             # Block root login
Port 2222                      # Change default port
MaxAuthTries 3                 # Limit attempts
ClientAliveInterval 300        # Timeout idle sessions
```

**Screenshot: After Configuration**

![SSHD After](images/week2_sshd_after.png)
*Caption: Hardened SSH configuration*

**Restart SSH:**
```bash
sudo systemctl restart sshd
```

---

### Firewall Configuration

#### Install and Configure UFW

**Commands:**
```bash
# Install firewall
sudo apt install ufw

# Set default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH from workstation only
sudo ufw allow from 192.168.10.3 to any port 2222

# Enable firewall
sudo ufw enable
```

**Screenshot: Firewall Setup**

![UFW Setup](images/week2_ufw_setup.png)
*Caption: Configuring firewall rules*

**Verify Status:**
```bash
sudo ufw status verbose
```

**Screenshot: Firewall Status**

![UFW Status](images/week2_ufw_status.png)
*Caption: Active firewall configuration*

---

### User and Privilege Management

#### Create Administrative User

**Commands:**
```bash
# Create user
sudo adduser adminuser

# Add to sudo group
sudo usermod -aG sudo adminuser

# Verify
groups adminuser
```

**Screenshot: User Creation**

![User Creation](images/week2_user_create.png)
*Caption: Creating non-root administrative user*

**Test sudo access:**
```bash
su - adminuser
sudo whoami
```

**Screenshot: Sudo Test**

![Sudo Test](images/week2_sudo_test.png)
*Caption: Verifying sudo privileges*

---

### Mandatory Access Control (AppArmor)

**Check status:**
```bash
sudo aa-status
```

**Screenshot: AppArmor Status**

![AppArmor](images/week2_apparmor.png)
*Caption: AppArmor profiles loaded and enforced*

---

### Automatic Security Updates

**Install and configure:**
```bash
# Install package
sudo apt install unattended-upgrades

# Enable automatic updates
sudo dpkg-reconfigure --priority=low unattended-upgrades

# Check status
sudo systemctl status unattended-upgrades
```

**Screenshot: Automatic Updates**

![Auto Updates](images/week2_auto_updates.png)
*Caption: Enabling automatic security updates*

---

## 3. Threat Model

### Threat 1: Brute Force SSH Attacks

**Description:** Attackers use automated tools (Hydra, John the Ripper) to guess passwords.

**Risk Level:** High

**Mitigations:**
- ✅ Disabled password authentication
- ✅ Enforced key-based auth only
- ✅ Changed SSH port to non-standard
- ✅ Limited SSH to workstation IP
- 🔜 Will implement fail2ban (Week 5)

**Effectiveness:** Eliminates password-based attacks entirely.

---

### Threat 2: Unauthorized Network Access

**Description:** Attackers scan for open ports using nmap and exploit discovered services.

**Risk Level:** Medium-High

**Mitigations:**
- ✅ Default deny firewall policy
- ✅ Whitelist workstation IP only
- ✅ Minimal service footprint (headless)
- ✅ Automatic security patching
- 🔜 Port scanning verification (Week 7)

**Effectiveness:** Significantly reduces attack surface.

---

### Threat 3: Privilege Escalation

**Description:** Attacker with limited access exploits vulnerabilities to gain root privileges.

**Risk Level:** Medium-High

**Mitigations:**
- ✅ Disabled root SSH login
- ✅ Created limited sudo user
- ✅ AppArmor confinement active
- ✅ Automatic security updates
- 🔜 Enhanced sudo logging (Week 5)

**Effectiveness:** Multiple layers force attackers to escalate through several controls.

---

## 4. System Documentation

### Network Configuration

**Command:**
```bash
ip addr show
```

**Screenshot: IP Configuration**

![IP Config](images/week2_ip_config.png)
*Caption: Server network interfaces and addressing*

---

### System Specifications

**Commands:**
```bash
uname -a
lsb_release -a
free -h
df -h
```

**Screenshot: System Info**

![System Info](images/week2_system_info.png)
*Caption: Kernel, distribution, memory, and disk information*

---

### Listening Ports

**Command:**
```bash
sudo ss -tlnp
```

**Screenshot: Open Ports**

![SS Listening](images/week2_ss_listening.png)
*Caption: Services listening on network ports*

---

## 5. Reflections

### Key Takeaways
- Ed25519 provides strong security with better performance than RSA
- Default deny firewall policy is essential for security
- Multiple security layers provide defense in depth
- Remote monitoring via SSH is efficient and lightweight

### Challenges
- Balancing security restrictions with system usability
- Ensuring firewall rules don't lock out legitimate access
- Understanding trade-offs between different monitoring tools

### Next Steps
- Week 3: Select applications for performance testing
- Week 4: Implement and test all security configurations
- Week 5: Add advanced security (fail2ban, monitoring scripts)

---

## References

[1] "OpenSSH Manual," OpenSSH, [Online]. Available: https://www.openssh.com/manual.html  
[2] "UFW Documentation," Ubuntu, [Online]. Available: https://help.ubuntu.com/community/UFW  
[3] "AppArmor Wiki," Ubuntu, [Online]. Available: https://wiki.ubuntu.com/AppArmor  
[4] "Ed25519 Signatures," cr.yp.to, [Online]. Available: https://ed25519.cr.yp.to/

---

**Last Updated:** [Date]  
**Status:** Week 2 Planning Complete
