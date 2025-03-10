![STIG-Hardened](https://i.imgur.com/BsQNMcw.png)

## Project Overview
This project focuses on achieving STIG compliance for a Red Hat Enterprise Linux (RHEL) server. We automate scanning, remediation, and validation of compliance using `Ansible` and the `SCAP Compliance Checker (SCC)` tool from DISA.

## Components
- **Control Node:** Rocky Linux 9 VM responsible for orchestrating scans and applying STIG compliance via `Ansible`
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
| Control(Rocky 9)  | Management / Orchestration Node   | 4   | 8 GB |
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

- **Dependencies**:
  - Python 3.9.21 and pip are installed along with required libraries:
    - boto3
    - botocore
    - Flask
    - requests 
  - Ansible 2.15.13  installed, configured, and ready for use
  - Terraform 1.10.5 installed and functional
  - Podman 5.2.2 installed for container management
- **AWS CLI Configuration**:
  - AWS credentials are set up using a shared credentials file, and the region is configured as us-east-1
  - The IAM user is verified via sts get-caller-identity, confirming its UserId, Account, and ARN
- **ECR Repository Status**:
  - Amazon Elastic Container Registry (ECR) repository named `breach-tracker` exists, and tagged as `breach-tracker-latest`
</details>

---
<br>

## Configure and Perform Initial Scan

**SSH into our rhel vm:**
```bash
ssh ansible@`<node1-ip>`
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
<summary> <h3>Findings Summary</h3> </summary>

![STIG-Hardened](https://i.imgur.com/urmgWhY.png) 
![STIG-Hardened](https://i.imgur.com/kkRl1qK.png)


Opening the non-compliance results at `/home/ansible/SCC/Sessions/` in a web browser, we can determine:
- Summary bullet point
- summary thoughts of analysis
- summary predictions
</details>

---

## Apply STIG Hardening Ansible Role and Validate

**Run `harden.yaml` playbook to automate remediation with pre-built Ansible role:**
```bash
ansible-playbook harden.yaml -vv
```
**Run `scan.sh` script to rescan and validate remediation:**
```bash
./scan.sh
```

<details close>
<summary> <h3>Findings Summary</h3> </summary>

![STIG-Hardened](https://i.imgur.com/oYsFy9C.png)
![STIG-Hardened](https://i.imgur.com/GeX8tJM.png)


Opening the non-compliance html results we can determine::
- Summary bullet point
- summary thoughts of analysis
- summary predictions
</details>

---

## Further Hardening with Custom Ansible Playbook

**Run `custom.yaml` playbook to further harden the system:**
```bash
ansible-playbook custom.yaml -vv
```
The playbook will further remediate the following STIGs:
  - **V-258018**: No unattended/automatic GUI login
  - **V-258094**: No null passwords
  - **V-257811**: Restrict ptrace usage
  - **V-257850, V-257851, V-257852**: Harden /home filesystem
  - **V-257861**: Harden /boot filesystem
  - **V-258125**: Ensure pcscd active
  - **V-258072, V-258073**: Enforce shell default permissions (umask)
  - **V-258068, V-258077**: Idle session auto-logout
  - **V-258007**: Disable X11 forwarding over SSH
  - **V-257985**: Disable remote SSH root login
  - **V-258042**: Password lifetime restriction (max 60 days)
  - **V-258105**: Minimum password lifetime (24 hours)
  - **V-258088**: Restrict su command to wheel

**Run `scan.sh` script to rescan and validate remediation:**
```bash
./scan.sh
```

<details close>
<summary> <h3>Findings Summary</h3> </summary>

![STIG-Hardened](https://i.imgur.com/b8gKXbR.png)
![STIG-Hardened](https://i.imgur.com/BklOCMW.png)


Opening the non-compliance html results we can determine::
- Summary bullet point
- summary thoughts of analysis
- summary predictions
</details>

---

## Conclusion

- ✅ Post-STIG Compliance Report (before/after results comparison)
- ✅ STIG Compliance Summary – Demonstrating compliance improvement.
