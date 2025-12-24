# Linux Secure Baseline: Hardening Rocky Linux 10

## 1. Project Overview
**Objective:** To manually harden a standard Rocky Linux 10 server instance to industry security standards, reducing the attack surface and establishing a defensible baseline.
**Tools Used:**
* **OS:** Rocky Linux 10
* **Hypervisor:** VMware Workstation Pro
* **Auditing Tool:** Lynis
* **Firewall:** Firewalld
* **Hardening Framework:** NIST / CIS Benchmarks (General Principles)

## 2. Methodology
The project followed a "Audit -> Remediation -> Verify" lifecycle:
1.  **Initial Audit:** Ran Lynis to establish a baseline score and identify vulnerabilities.
2.  **Hardening:** Applied security controls across Network, SSH, and System configurations.
3.  **Final Audit:** Re-ran Lynis to verify improvements and ensure no critical services were broken.

## 3. Quantitative Results (Metrics)
| Metric | Initial State (Fresh Install) | Final State (Hardened) |
| :--- | :--- | :--- |
| **Lynis Hardening Index** | **66** (Example) | **72** (Example) |
| **Total Warnings** | 25 | 3 |
| **Firewall Status** | Inactive | Active (Public Zone Restricted) |

## 4. Key Hardening Implementation
### A. SSH & Network Security
* **Disabled Root Login:** Prevented direct root access via SSH (`PermitRootLogin no`).
* **Changed Default SSH Port:** Moved from 22 to [Your Port] to reduce scanner noise.
* **Firewall Configuration:** Configured `firewalld` to only allow traffic on specific ports (SSH, HTTP).

### B. Account & Authentication
* **Password Policies:** Enforced strong password complexity using `pwquality`.
* **Sudo Access:** Restricted `sudo` privileges to the administrative user only.
* **Idle Session Timeout:** Configured shell to logout inactive users after 5 minutes.

### C. System & Kernel
* **Banner Messages:** Added a legal warning banner to `/etc/issue` for compliance.
* **USB Storage:** Disabled USB storage drivers to prevent physical data theft.

## 5. Challenges & Troubleshooting
* *Issue:* After enabling the firewall, I lost SSH access.
* *Resolution:* I had to access the VM via the VMware console to manually add the custom SSH port to the `firewalld` allowed list.

## 6. Evidence
Below are the screenshots verifying the hardening steps. Click the links to view the evidence.

### Initial Assessment
* [Task 1.1 - Initial Audit Score (Prior to Hardening)](Evidence/Task%201.1.png)
* [Task 1.2 - SSH Connection Test](Evidence/Task%201.2.png)
* [Task 1.3 - Initial IP Configuration](Evidence/Task%201.3.png)
* [Task 1.4 - System Version Check](Evidence/Task%201.4.png)
* [Task 1.5 - User Account Check](Evidence/Task%201.5.png)

### Hardening Implementation
* [Task 2.1 - Firewall Configuration](Evidence/Task%202.1.png)
* [Task 4.1 - SSH Config Updates](Evidence/Task%204.1.png)
* [Task 4.2 - Root Login Disabled](Evidence/Task%204.2.png)
* [Task 4.3 - SSH Port Change Verification](Evidence/Task%204.3.png)
* [Task 4.4 - SSH Service Restart](Evidence/Task%204.4.png)

### Verification
* [Task 10.1 - Final Audit Score (Post Hardening)](Evidence/Task%2010.1.png)

## 7. Future Scope
To scale this project, I plan to convert these manual hardening steps into an **Ansible Playbook** to automate the deployment of secure baselines across multiple servers simultaneously.
