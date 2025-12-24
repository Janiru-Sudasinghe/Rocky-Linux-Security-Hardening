# Linux Secure Baseline Build & Verify

![Platform](https://img.shields.io/badge/Platform-Rocky%20Linux%2010-blue)
![Tools](https://img.shields.io/badge/Tools-Lynis%20|%20Auditd%20|%20Firewalld-green)
![Status](https://img.shields.io/badge/Status-Completed-success)

## 📋 Project Overview
This project involves the manual hardening of a generic Linux server (Rocky Linux 10) to establish a secure baseline. The objective was to transform a fresh install into a defensible system by implementing "Least Privilege" principles, minimizing the attack surface, and establishing audit trails.

The efficacy of the hardening process was measured quantitatively using **Lynis**, comparing the security posture before and after the applied controls.

## 🛠 Architecture & Technologies
* **OS:** Rocky Linux 10 (RHEL-compatible)
* **Hypervisor:** VMware Workstation Pro
* **Key Security Tools:**
    * **Firewalld:** Network zone management.
    * **OpenSSH:** Secure remote administration.
    * **Auditd:** Kernel-level event auditing.
    * **DNF-Automatic:** Automated patch management.
    * **Lynis:** Compliance and vulnerability auditing.

---

## 🔐 Configuration Breakdown

### Task 1: Identity & Access Management (IAM)
* **User Creation:** Established a dedicated administrative user (`student`) added to the `wheel` group for `sudo` access.
* **SSH Key Implementation:** Generated RSA-2048 key pairs to replace password-based authentication.
* **Verification:** Confirmed passwordless entry and appropriate `authorized_keys` permissions.

### Task 2: Baseline Security Audit
* **Tool Used:** Lynis.
* **Process:** Conducted an initial system audit on the "fresh" install to establish a baseline score.
* **Outcome:** Identified critical gaps in firewall configuration, SSH settings, and kernel parameters.

### Task 3: Network Security (Firewalld)
* **Strategy:** "Deny All, Permit by Exception."
* **Configuration:**
    * Enabled `firewalld` and restricted the public zone.
    * Removed unnecessary services (e.g., `dhcpv6-client`).
    * Explicitly allowed only SSH traffic (Port 22).
* **Validation:** Verified using `nmap` from an external client to ensure no unauthorized ports were visible.

### Task 4: SSH Hardening
* **Config File:** `/etc/ssh/sshd_config`
* **Key Controls Applied:**
    * `Protocol 2`: Enforce modern SSH protocol.
    * `PermitRootLogin no`: Disabled direct root access.
    * `PasswordAuthentication no`: Enforced key-based auth only.
    * `MaxAuthTries 3`: Mitigated brute-force attempts.
    * `ClientAliveInterval 300`: Configured idle session timeouts.

### Task 5: Automated Patch Management
* **Tool:** `dnf-automatic`.
* **Policy:** Configured the system to automatically download and apply **security** updates only (`upgrade_type = security`).
* **Monitoring:** Enabled systemd timers to run updates daily and log results to `journalctl`.

### Task 6: System Auditing & Logging
* **Tool:** Linux Audit Daemon (`auditd`).
* **Rules Implemented:**
    * **Identity Tracking:** Watched `/etc/passwd` and `/etc/group` for unauthorized user modification.
    * **Privilege Escalation:** Monitored `/etc/sudoers` for changes to admin rights.
    * **Config Tampering:** Tagged writes to `/etc/ssh/sshd_config`.
* **Testing:** Triggered deliberate events (user creation) and verified detection via `ausearch`.

### Task 7: File System Permissions
* **Global Umask:** Set default umask to `027` (rwxr-x---) via `/etc/profile.d/`, ensuring new files are not world-readable by default.
* **Shared Directories:** Created a collaboration folder `/srv/projshare` using **SGID** (Set Group ID) to ensure inherited group ownership.
* **Sticky Bit:** Verified integrity of `/tmp` permissions to prevent user file deletion.

### Task 8: Kernel Hardening
* **Config File:** `/etc/sysctl.d/99-security.conf`
* **Network Stack Adjustments:**
    * Disabled IP Forwarding and Source Routing.
    * Enabled SYN Cookies (`net.ipv4.tcp_syncookies = 1`) to mitigate DoS attacks.
    * Enabled Reverse Path Filtering (`rp_filter`) to prevent IP spoofing.
    * Ignored ICMP Broadcast requests to prevent Smurf attacks.

### Task 9: Verification 
* **Final Audit:** Re-ran Lynis to calculate the hardening delta.

| Metric | Initial State (Fresh Install) | Final State (Hardened) |
| :--- | :--- | :--- |
| **Lynis Hardening Index** | **66**  | **72**  |
| **Total Warnings** | 25 | 3 |
| **Firewall Status** | Inactive | Active (Public Zone Restricted) |

### Task 10: Intrusion Detection
* **"Break & Detect" Scenario:**
    * **Action:** Deliberately weakened security by changing `PasswordAuthentication` to `yes` in `/etc/ssh/sshd_config` to simulate an unauthorized configuration change.
    * **Detection:** Successfully identified the file modification event using `auditd` logs via the command: `sudo ausearch -k ssh_config`.
    * **Remediation:** Reverted the setting to `no` and restarted the SSH daemon to restore the secure baseline.

---

## 📂 Repository Structure

```text
├── configs/               # Hardened configuration files (sanitized)
│   ├── sshd_config
│   ├── hardening.rules
│   └── 99-security.conf
├── evidence/              # Screenshots of verification steps
├── reports/               # Lynis audit reports (Before/After)
└── README.md              # Project documentation
