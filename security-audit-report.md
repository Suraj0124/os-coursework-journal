# Security Audit Report

**System:** ubuntuserverr (192.168.56.103)  
**Operating System:** Ubuntu Server 24.04 LTS  
**Audit Date:** 17–18 December 2025  
**Auditor:** Suraj

---

## Executive Summary

This report documents a comprehensive security audit of an Ubuntu Server 24.04 LTS virtual machine. The audit included automated scanning with Lynis, network security assessment with nmap, service inventory analysis, firewall and access control verification, and systematic hardening implementation.

### Key Findings

- **Initial hardening index:** 65/100
- **Final hardening index:** 68/100
- **Improvement:** +3 points (4.6% increase)
- **Critical vulnerabilities addressed:** 3
- **Services minimised:** 0 disabled (already minimal baseline)

The system demonstrated a strong baseline security posture, with measurable improvement after targeted hardening actions.

---

## Audit Methodology

### Tools and Techniques

- **Lynis** – Comprehensive system security audit and scoring
- **nmap** – Network port and service scanning
- **Manual review** – Configuration file analysis
- **Service inventory** – Running and listening service assessment
- **Custom verification** – Firewall, sysctl, and audit logging checks

### Scope

- Operating system configuration
- Network security
- Access controls
- Authentication mechanisms
- Service security
- Kernel hardening

---

## Initial Security Assessment

### Lynis Initial Audit Results

- **Hardening index:** 65/100
- **Total tests performed:** 262
- **Warnings identified:** 0
- **Suggestions made:** 48

### Critical Issues Identified

1. **Weak default password policy** – Insufficient enforcement of complexity and length increases risk of brute-force attacks
2. **Audit logging not fully enabled** – Limited forensic and incident investigation capability
3. **Kernel network hardening incomplete** – Potential exposure to spoofing and redirect-based attacks

---

## Network Security Assessment

### Open Ports Identified

- **Port 22 (SSH):** Required for secure remote administration
- **Port 80 (HTTP):** Required for Apache web service testing

All other ports were confirmed closed or filtered.

### Services Listening

- **sshd:** Secure remote access (restricted by firewall)
- **apache2:** Web service (required for coursework)
- **systemd-resolved:** Local DNS resolver (loopback only)

Risk level assessed as low, due to strict firewall rules and minimal exposure.

---

## Security Hardening Implemented

### 1. SSH Hardening

- ✓ Key-based authentication enabled
- ✓ Password authentication disabled
- ✓ Root login disabled
- ✓ Default port maintained (22)

**Impact:** Eliminates brute-force password attack risk.

### 2. Firewall Configuration

- ✓ UFW enabled with deny-by-default policy
- ✓ SSH restricted to trusted workstation IP (192.168.56.105)
- ✓ All unnecessary ports blocked

**Impact:** Significantly reduced external attack surface.

### 3. Intrusion Detection

- ✓ fail2ban installed and active
- ✓ SSH jail enabled
- ✓ Automated banning of brute-force attempts

**Impact:** Active protection against automated attacks.

### 4. Mandatory Access Control

- ✓ AppArmor enabled and running
- ✓ Default Ubuntu security profiles enforced

**Impact:** Additional mandatory access control layer beyond DAC.

### 5. Automatic Security Updates

- ✓ unattended-upgrades configured
- ✓ Automatic security updates enabled

**Impact:** Timely patching of known vulnerabilities.

### 6. Kernel Hardening

Implemented kernel security parameters:

- IP forwarding disabled
- SYN cookies enabled (anti-DoS)
- ICMP redirects disabled
- Source routing disabled
- IP spoofing protection enabled
- Martian packet logging enabled

**Impact:** Protection against common network-level attacks.

### 7. File System Security

- Shared memory secured (noexec, nosuid, nodev)
- SUID files reviewed and justified
- USB storage disabled

**Impact:** Reduced privilege escalation opportunities.

### 8. Password Policy Enhancement

- Minimum length: 12 characters
- Complexity enforced (upper, lower, digit, special)
- Password ageing configured (max 90 days)

**Impact:** Stronger authentication and credential protection.

### 9. Service Minimisation

- **Before hardening:** 21 running services
- **After hardening:** 21 running services
- No unnecessary services were identified for removal.

**Impact:** Minimal attack surface already in place.

### 10. Audit Logging

- auditd installed and enabled
- audispd plugins configured
- Log rotation verified

**Impact:** Improved incident detection and forensic capability.

---

## Post-Hardening Assessment

### Lynis Final Audit Results

- **Hardening index:** 68/100
- **Improvement:** +3 points
- **Warnings resolved:** 0
- **Remaining warnings:** 0

### Comparison Analysis

| Metric | Before | After |
|--------|--------|-------|
| Hardening Index | 65 | 68 |
| Tests Performed | 262 | 267 |
| Warnings | 0 | 0 |
| Suggestions | 48 | Reduced |

### Most Impactful Improvements

1. Password policy enforcement
2. Kernel network hardening
3. Audit logging enablement

---

## Remaining Risks and Limitations

### Accepted Risks

**No malware scanner installed**
- **Justification:** Isolated VM with limited exposure
- **Mitigation:** Minimal services and firewall controls

**HTTP service exposed (port 80)**
- **Justification:** Required for coursework
- **Mitigation:** Keep Apache patched and minimal modules enabled

### Recommendations for Future Improvement

1. Enable HTTPS (TLS) for Apache
2. Install ClamAV or equivalent malware scanner
3. Continue addressing Lynis suggestions iteratively

---

## Security Control Verification

All security controls verified functional as of 18 Dec 2025:

- [✓] SSH security
- [✓] Firewall protection (UFW active)
- [✓] Intrusion detection (fail2ban)
- [✓] Mandatory access control (AppArmor)
- [✓] Automatic updates
- [✓] Kernel hardening
- [✓] Service minimisation
- [✓] Audit logging

---

## Conclusion

This security audit successfully identified and addressed three key security weaknesses, improving the system's hardening index from 65/100 to 68/100, representing a 4.6% improvement.

The most significant improvements were achieved through:

1. Password policy hardening
2. Kernel network parameter tuning
3. Audit logging enablement

While some low-risk items remain, these have been formally assessed and accepted. The system now follows defence-in-depth principles and aligns well with industry best practices for a controlled Ubuntu Server environment.

---


