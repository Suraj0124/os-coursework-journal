# Phase 4: Initial System Configuration & Security Implementation (Week 4)

**Platform:** Ubuntu Server (Ubuntu Linux)

This document records the deployment, configuration, and foundational security implementation performed on the Ubuntu server during Phase 4. It includes command evidence, configuration changes, and security controls as required.

---

## 1. Server Deployment Overview

* **OS:** Ubuntu Server (LTS)
* **Access Method:** SSH
* **Purpose:** Secure remote administration with least-privilege access

---

## 2. SSH Configuration with Key-Based Authentication

### 2.1 Generate SSH Key Pair (Client Workstation)

```bash
ssh-keygen -t ed25519 -C "admin-workstation"
```

* Private key stored locally
* Public key used for server authentication

### 2.2 Copy Public Key to Server

```bash
ssh-copy-id adminuser@<server-ip>
```

Alternatively:

```bash
cat ~/.ssh/id_ed25519.pub | ssh adminuser@<server-ip> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### 2.3 SSH Daemon Configuration

**File:** `/etc/ssh/sshd_config`

#### Before Configuration

```text
#PasswordAuthentication yes
#PubkeyAuthentication yes
PermitRootLogin yes
```

#### After Configuration

```text
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no
```

Restart SSH service:

```bash
sudo systemctl restart ssh
```

---

## 3. Firewall Configuration (Restricted SSH Access)

### 3.1 Firewall Tool Used

* **UFW (Uncomplicated Firewall)**

### 3.2 Default Firewall Policies

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

### 3.3 Allow SSH from Specific Workstation Only

```bash
sudo ufw allow from <workstation-ip> to any port 22 proto tcp
```

### 3.4 Enable Firewall

```bash
sudo ufw enable
```

### 3.5 Firewall Ruleset Verification

```bash
sudo ufw status verbose
```

**Expected Output (Example):**

```text
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       <workstation-ip>
```

---

## 4. User Management & Privilege Configuration

### 4.1 Create Non-Root Administrative User

```bash
sudo adduser adminuser
```

### 4.2 Grant Sudo Privileges

```bash
sudo usermod -aG sudo adminuser
```

Verify group membership:

```bash
groups adminuser
```

### 4.3 Disable Root SSH Login

Configured in `/etc/ssh/sshd_config`:

```text
PermitRootLogin no
```

---

## 5. SSH Access Evidence

### 5.1 Successful SSH Login (Key-Based)

```bash
ssh adminuser@<server-ip>
```

**Evidence to Attach:**

* Screenshot showing successful login without password prompt
* Terminal prompt displaying logged-in username and hostname

---

## 6. Configuration File Evidence (Before & After)

### 6.1 SSH Configuration File

**File:** `/etc/ssh/sshd_config`

| Setting                | Before          | After |
| ---------------------- | --------------- | ----- |
| PasswordAuthentication | yes (commented) | no    |
| PubkeyAuthentication   | commented       | yes   |
| PermitRootLogin        | yes             | no    |

---

## 7. Firewall Documentation

### 7.1 Complete Firewall Ruleset

```bash
sudo ufw status numbered
```

**Documented Rules:**

1. Default deny incoming
2. Allow SSH only from trusted workstation IP
3. Outgoing traffic allowed

---

## 8. Remote Administration Evidence (via SSH)

### 8.1 Commands Executed Remotely

```bash
uname -a
uptime
whoami
sudo apt update
```

**Evidence to Attach:**

* Screenshot showing commands executed over SSH
* Output confirming administrative privileges via `sudo`

---

## 9. Security Summary

* Root login disabled
* Password-based SSH authentication disabled
* SSH access restricted to trusted workstation
* Non-root administrative user implemented
* Firewall actively enforcing least-privilege access

---

## 10. Conclusion

Phase 4 successfully established secure remote administration and foundational system hardening on the Ubuntu server. These controls significantly reduce attack surface and align with security best practices for Linux server deployment.

---

**End of Phase 4 Documentation**

