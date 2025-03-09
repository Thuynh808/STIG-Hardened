![STIG-Hardened](https://i.imgur.com/BsQNMcw.png)

## Project Overview
- Identify which operating systems (RHEL, Windows Server, Ubuntu) and applications (Apache, SQL Server) need STIG compliance.
- Determine the STIG benchmark version (latest from DISA cyber.mil).
- Clarify if we are targeting DoD STIG Level 1 (basic security) or Level 2 (higher security with more restrictions).

## Components
- **Control Node:** Rocky vm used to setup, scan, and remediate for stig compliance
- **Node1:** Rhel9 vm as our target machine to make stig compliant
- **Ansible:** to automate setup, scan, and remediate with playbooks
- **SCC:** scap scanner for scanner for compliance

## Prerequisites
Before we begin, ensure the following are prepared:
- 1 Rocky 9 VM
- 1 RHEL 9 VM with registered account for repository access 

| Server            | Role                            | CPU | RAM  |
|-------------------|---------------------------------|-----|------|
| Control(Rocky 9)  | Management                      | 4   | 8 GB |
| Node1(rhel 9)     | Target vm for compliance        | 4   | 8 GB |     

> Note: Setup vms on same network. root password set to `password`

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
  - Generates a SHA-512 hashed password and stores it as a fact for later use. Only runs on the control node.
  - Ensures the Roles and scc directories exist with correct permissions
  - Downloads STIG compliance files and the SCC tool bundle from DoD's website
  - Extracts the downloaded Ansible STIG role into the SCC directory
  - Moves the extracted RHEL 9 STIG role into the Roles directory
  - Creates a Bash script (scan.sh) that remotely executes SCC on node1
  - Adds the ansible user to all target RHEL 9 nodes with the pre-generated password hash
  - Adds ansible user to /etc/sudoers.d/ with passwordless sudo access on RHEL 9 nodes
  - Enables passwordless SSH access for the ansible user by generating and copying the control node's public key to RHEL 9 nodes
  - Transfers the SCC installation package from the control node to RHEL 9 nodes
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
    
![STIG-Hardened](https://i.imgur.com/E7iWTvv.png)

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

![STIG-Hardened](https://i.imgur.com/BsQNMcw.png)

Opening the non-compliance html results we can determine::
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

![STIG-Hardened](https://i.imgur.com/BsQNMcw.png)

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
The playbook will:
  - Install collections from requirements file
  - Generate root SSH keypair

**Run `scan.sh` script to rescan and validate remediation:**
```bash
./scan.sh
```

<details close>
<summary> <h3>Findings Summary</h3> </summary>

![STIG-Hardened](https://i.imgur.com/BsQNMcw.png)

Opening the non-compliance html results we can determine::
- Summary bullet point
- summary thoughts of analysis
- summary predictions
</details>

---

## Conclusion

- ✅ Post-STIG Compliance Report (before/after results comparison)
- ✅ STIG Compliance Summary – Demonstrating compliance improvement.
