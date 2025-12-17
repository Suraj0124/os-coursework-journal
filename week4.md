# Phase 4: Initial System Configuration & Security Implementation (Week 4)

## Reflective Overview

Week 4 marked a significant transition from planning to hands-on implementation of security controls on the Ubuntu Server. During this phase, I focused on applying the core security principles identified earlier in the project, particularly around securing remote access, reducing unnecessary network exposure, and enforcing the principle of least privilege.

All tasks were deliberately carried out remotely via SSH, without relying on local console access. This constraint closely mirrored real-world server administration scenarios, where administrators are expected to manage systems securely over the network. Working exclusively through SSH required careful planning and validation at each step, as misconfiguration could have resulted in loss of access.

Through this process, I gained practical experience in hardening SSH access using key-based authentication, implementing restrictive firewall rules, and managing user privileges effectively. Overall, this phase reinforced the importance of cautious, methodical configuration when securing production-like systems and highlighted how foundational security measures significantly reduce the server's attack surface.

---

## 1. SSH Configuration with Key-Based Authentication

### Objective

Configure SSH to use key-based authentication and disable password-based login.

### Steps Performed

* Generated SSH key pair on the workstation
* Copied public key to the Ubuntu server
* Updated SSH daemon configuration
* Restarted SSH service

### Commands Used

```bash
# Commands executed from the workstation
ssh-keygen -t ed25519 -C "admin@workstation"
ssh-copy-id adminuser@<server_ip>

sudo nano /etc/ssh/sshd_config
sudo systemctl restart ssh
```

### Configuration File Changes

**File:** `/etc/ssh/sshd_config`

#### Before Configuration

```text
# Default SSH configuration (excerpt)
#PasswordAuthentication yes
#PermitRootLogin prohibit-password
```

#### After Configuration

```text
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin no
```

### Screenshot Evidence

**SSH Key Generation:**

![SSH Key Generation](ssh-key.png)

**SSH Key Copy:**

![SSH Key Copy to Server](ssh-key-copy-id.png)

**SSH Key-Based Authentication:**

![SSH Key-Based Authentication](ssh-key-auth.png)

---

## 2. Firewall Configuration (Restricted SSH Access)

### Objective

Configure a firewall allowing SSH access **only from a specific workstation IP address**.

### Firewall Tool Used

* UFW (Uncomplicated Firewall)

### Steps Performed

* Enabled UFW
* Allowed SSH access from workstation IP only
* Denied all other incoming connections by default

### Commands Used

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from <workstation_ip> to any port 22 proto tcp
sudo ufw enable
```

### Complete Firewall Ruleset

```text
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       <workstation_ip>

Default: deny (incoming), allow (outgoing), disabled (routed)
```

### Screenshot Evidence

![Firewall Rules Configuration](firewall-rules.png)

---

## 3. User Management & Privilege Configuration

### Objective

Create a non-root administrative user and implement proper privilege management.

### Steps Performed

* Created a new non-root user
* Added user to `sudo` group
* Disabled direct root SSH login

### Commands Used

```bash
sudo adduser adminuser
sudo usermod -aG sudo adminuser
sudo passwd -l root
```

### Verification

* Logged in as non-root user via SSH
* Verified sudo privileges

### Screenshot Evidence

![User Privileges Configuration](user-privileges.png)

---

## 4. SSH Access Evidence

### Objective

Demonstrate successful remote SSH access from the workstation.

### Evidence

* SSH login prompt
* Successful connection confirmation

### Screenshot Evidence

![SSH Access from Workstation](ssh-access.png)

---

## 5. Configuration Files – Before & After Comparison

### Files Modified

* `/etc/ssh/sshd_config`
* `/etc/ufw/user.rules` (if applicable)

### Comparison Evidence

#### Before Configuration

![Configuration Before Changes](before.png)

#### After Configuration

![Configuration After Changes](after.png)

---

## 6. Firewall Documentation

### Default Policies

* Incoming: Deny
* Outgoing: Allow

### Allowed Services

| Service | Port | Source IP        |
| ------- | ---- | ---------------- |
| SSH     | 22   | <Workstation_IP> |

### Screenshot Evidence

![Firewall Rules Status](firewall-rules.png)

---

## 7. Remote Administration Evidence

### Objective

Demonstrate administrative commands executed remotely via SSH.

### Commands Executed

```bash
sudo apt update
sudo apt upgrade -y
sudo ufw status verbose
whoami
```

### Screenshot Evidence

![Remote Administration Commands](remote-admin.png)

---

## Conclusion

This phase successfully implemented foundational system security by securing SSH access, restricting network access through firewall rules, and enforcing least-privilege administration using a non-root user. All configurations were performed remotely via SSH in compliance with the administrative constraints.

The implementation demonstrated the practical application of security principles including:
- **Defense in Depth**: Multiple layers of security (SSH keys, firewall, user privileges)
- **Principle of Least Privilege**: Non-root administrative access with sudo
- **Secure Remote Access**: Key-based authentication with restricted IP access
- **Network Segmentation**: Firewall rules limiting exposure to authorized sources only

These foundational controls significantly reduced the server's attack surface and established a secure baseline for future system operations.
