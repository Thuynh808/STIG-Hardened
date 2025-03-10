![STIG-Hardened](https://i.imgur.com/dhp33At.png)

## Project Overview
This project focuses on achieving at least 80% STIG compliance for a Red Hat Enterprise Linux (RHEL) server. We automate scanning, remediation, and validation of compliance using `Ansible` and the `SCAP Compliance Checker (SCC)` tool from DISA.

## Components
- **Control Node:** Rocky Linux 9 VM responsible for orchestrating scans and applying STIG compliance via Ansible
- **Target Node (Node1):** RHEL 9 VM targeted for STIG compliance scanning and remediation
- **Ansible:** Automation tool used to manage setup, scanning, and remediation via playbooks and roles
- **SCC:** Official DISA-provided tool to scan systems for STIG compliance
- **RHEL 9 STIG:** Benchmark Version 2.2.3, based on STIG Manual Version 2.2, dated 2024-10-28 (Benchmark Date: 2024-10-24)
    

## Prerequisites
Before we begin, ensure the following are prepared:
- 1 Rocky 9 VM
- 1 RHEL 9 VM with registered account for repository access 

| Server            | Role                              | CPU | RAM  |
|-------------------|-----------------------------------|-----|------|
| Control(Rocky 9)  | Management/Orchestration Node     | 4   | 8 GB |
| Node1(rhel 9)     | Target System for STIG Compliance | 4   | 8 GB |     

> Note: VMs should be on the same network. Set the root password as *`password`* for initial setup
<br>

## Setup Environment
  
**Install `git` and `ansible-core`:**
```bash
dnf install -y git ansible-core
```
**Clone the repository and Install Ansible collections:**
```bash
git clone https://github.com/Thuynh808/STIG-Hardened
cd STIG-Hardened
ansible-galaxy install -r requirements.yaml
```
**Configure inventory `hosts` with proper IPs:**
```bash
vim inventory
```
**Run setup.yaml playbook:**
```bash
ansible-playbook setup.yaml -vv
```
The playbook will:
  - Generates a SHA-512 hashed password and stores it as a fact for later use. Only runs on the control node
  - Ensures the Roles and scc directories exist with correct permissions
  - Downloads STIG compliance files and the SCC tool bundle from DoD's website
  - Extracts the downloaded Ansible STIG role into the SCC directory
  - Moves the extracted RHEL 9 STIG role into the Roles directory
  - Creates a Bash script (scan.sh) that remotely executes SCC on node1
  - Adds the ansible user to all target RHEL 9 nodes with the pre-generated password hash
  - Adds ansible user to /etc/sudoers.d/ with passwordless sudo access on RHEL 9 node
  - Enables passwordless SSH for ansible by generating and copying the control node's public key to RHEL 9 node
  - Transfers the SCC installation package from the control node to RHEL 9 node
  - Installs SCC using dnf on RHEL 9 nodes
  - Modifies ansible.cfg to disable the ask_pass setting
  - Updates ansible.cfg to use ansible as the default remote user

**Confirm Successful Execution:**
```bash
git --version
ansible --version | head -n 5
ll Roles scc
cat scan.sh
ansible rhel9 -m ping
ansible rhel9 -m shell -a "cat /etc/passwd | grep -i ansible"
ansible rhel9 -m shell -a "sudo cat /etc/sudoers.d/ansible"
ansible rhel9 -m shell -a "sudo cat ~/.ssh/authorized_keys"
ansible rhel9 -m shell -a "sudo /opt/scc/cscc --version"
```

<details close>
<summary> <h3>Image Results</h3> </summary>
    
![STIG-Hardened](https://i.imgur.com/BqTeHSI.png)

- ✅ **Verified Git and Ansible installations**: Confirmed git version 2.43.5 and ansible core 2.14.17
- ✅ **Roles and SCC directories**: `Roles/` contains `rhel9STIG` role, and `scc/` contains Ansible files, SCC package, and STIG documentation
- ✅ **scan.sh script created**: Script designed to run SCC remotely over SSH, shown with correct syntax and permissions
- ✅ **Ansible and user setup validated**: Successful ping response from `rhel9` target and correct ansible user presence in `/etc/passwd`
- ✅ **Sudo configured for Ansible user**: Verified entry in `/etc/sudoers.d/ansible` allowing `NOPASSWD:ALL` access
- ✅ **Passwordless SSH confirmed**: Public SSH key properly installed in `~/.ssh/authorized_keys` on rhel9 target
- ✅ **SCC tool installed**: Verified `/opt/scc/cscc` version output showing correct installation
</details>

---
<br>

## Configure and Perform Initial Scan

**SSH into our rhel vm:**
```bash
ssh ansible@<node1-ip>
```
**Configure scanning options:**  
```bash
sudo /opt/scc/cscc --config
```
- Hit `Enter` to acknowledge the change log
- Hit `1` and `Enter` to select `Configure SCAP content`
- Hit `1-8` and `Enter` to deselect all content
- Hit `6` and `Enter` to select `RHEL_9_STIG`
- Hit `0` and `Enter` to return to the main menu
- Hit `6` and `Enter` to select `Configuration Options`
- Hit `1` and `Enter` to select `Scanning Options`
- Hit `1` and `Enter` to select `Run all SCAP content regardless of applicability`
- Hit `0` and `Enter` to return to the previous menu
- Hit `0` and `Enter` to return to the main menu
- Hit `9` and `Enter` to exit, save changes, and perform scan

<details close>
<summary> <h3>Results Summary</h3> </summary>

![STIG-Hardened](https://i.imgur.com/urmgWhY.png) 
![STIG-Hardened](https://i.imgur.com/kkRl1qK.png)

**Initial STIG Compliance Scan Summary**
- Reviewed non-compliance HTML report generated by SCAP Compliance Checker (SCC)
- Initial compliance score: **`35.4%`** (RED - Non-Compliant)
- Indicates a high number of critical (CAT I) and medium (CAT II) severity findings
- System requires significant remediation to meet RHEL 9 STIG standards
> Note: Full detailed report is available in /home/ansible/SCC/Sessions/
</details>

---
<br>

## Apply STIG Hardening Ansible Role and Validate

The STIG Ansible role used in this step is the official DISA STIG role for RHEL 9, provided via cyber.mil. This role includes pre-defined tasks and configurations aligned with the Department of Defense (DoD) Security Technical Implementation Guide (STIG) standards.
<br>

**Run `harden.yaml` playbook to automate remediation with pre-built Ansible role:**
```bash
ansible-playbook harden.yaml -vv
```
**Run `scan.sh` script to rescan and validate remediation:**
```bash
./scan.sh
```

<details close>
<summary> <h3>Results Summary</h3> </summary>

![STIG-Hardened](https://i.imgur.com/oYsFy9C.png)
![STIG-Hardened](https://i.imgur.com/GeX8tJM.png)

**Post-Remediation STIG Compliance Scan Summary**
- Reviewed updated non-compliance HTML report generated by SCAP Compliance Checker (SCC)
- Post-remediation compliance score: **`78.96%`** (RED - Still below passing but significantly improved)
- Indicates a reduced number of critical (CAT I) and medium (CAT II) severity findings
- System shows substantial improvement but requires additional hardening to fully meet RHEL 9 STIG standards
> Note: Full detailed report is available in /home/ansible/SCC/Sessions/
</details>

---
<br>

## Further Hardening with Custom Ansible Playbook

The following playbook addresses additional STIG findings that were not fully remediated by the initial DISA role, including filesystem hardening, login restrictions, and session security.
<br>

**Run `custom.yaml` playbook to further harden the system:**
```bash
ansible-playbook custom.yaml -vv
```
The playbook will further remediate the following STIGs:
  - **V-258018**: Disable unattended/automatic GUI login
  - **V-258094**: Disallow null passwords
  - **V-257811**: Restrict `ptrace` usage
  - **V-257850, V-257851, V-257852**: Harden `/home` filesystem
  - **V-257861**: Harden `/boot` filesystem
  - **V-258125**: Ensure `pcscd` active
  - **V-258072, V-258073**: Enforce default shell permissions (`umask`)
  - **V-258068, V-258077**: Configure idle session auto-logout
  - **V-258007**: Disable X11 forwarding over SSH
  - **V-257985**: Disable SSH root login
  - **V-258042**: Enforce password maximum lifetime (60 days)
  - **V-258105**: Enforce minimum password lifetime (24 hours)
  - **V-258088**: Restrict `su` command to `wheel` group

**Run `scan.sh` script to rescan and validate remediation:**
```bash
./scan.sh
```

<details close>
<summary> <h3>Results Summary</h3> </summary>

![STIG-Hardened](https://i.imgur.com/b8gKXbR.png)
![STIG-Hardened](https://i.imgur.com/BklOCMW.png)


**Final STIG Compliance Scan Summary (Post-Custom Playbook)**
- Reviewed final non-compliance HTML report generated by SCAP Compliance Checker (SCC)
- Final compliance score: **`82.18%`** (YELLOW - Moderate Compliance)
- Indicates further reduction of critical (CAT I) and medium (CAT II) severity findings after applying custom playbook
> Note: Full detailed report is available in /home/ansible/SCC/Sessions/
</details>

---
<br>

## Conclusion

This project successfully demonstrates a **realistic**, **automated workflow** for achieving STIG compliance on a RHEL 9 system using **Ansible and SCC**. Through multiple rounds of scanning, remediation, and validation, we met our target compliance goal and showcased a practical approach to system hardening aligned with DISA STIG standards.

- **Achieved Goal**: Successfully reached **82.18% STIG compliance**, meeting and exceeding the **80%** compliance goal set for this project
- **Demonstrated Progress**: Improved from a **35.4% compliance score** to over **80%**, showing significant remediation and system hardening
- **Actionable Framework**: Established an **automated, repeatable process** for scanning, remediating, and validating STIG compliance
- **Future Considerations**: While **82% compliance** is sufficient for **demonstration and practice**, further remediation of remaining findings can be performed based on **specific organizational or mission requirements**

> ⚠️ Disclaimer:
> SCC is an official DISA-provided tool available only to authorized DoD personnel, contractors, and approved organizations via cyber.mil.
> No SCC software or full reports are distributed with this project. This project is intended for personal use and skills demonstration only, and to showcase familiarity with DISA STIG compliance processes.
