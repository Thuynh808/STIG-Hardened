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
- 1 RHEL 9 VM


| Server           | Role                            | CPU | RAM  |
|------------------|---------------------------------|-----|------|
| Control(Rocky 9)  | Management                     | 4   | 8 GB |
| Node1(rhel 9)    | Target vm for compliance        | 4   | 8 GB |     

## Setup Environment
  
- **Install `git` and `ansible-core`**
  
  ```bash
  dnf install -y git ansible-core
  ```
- **Clone the repository**
  
  ```bash
  git clone https://github.com/Thuynh808/STIG-Hardened
  cd STIG-Hardened
  ```
- **Configure inventory `hosts`**
  
  ```bash
  vim inventory
  ```
- **Run setup.yaml playbook**
  
  ```bash
  ansible-playbook setup.yaml -vv
  ```
  <details close>
  <summary> <h4>Playbook Breakdown</h4> </summary>
    
  - Install collections from requirements file
  - Generate root SSH keypair

  </details>

## Configure and Perform Initial Scan

- SSH into our rhel vm:
```bash
ssh ansible@`<rhel-stig-ip>`
```
- Configure scanning options:  
```bash
sudo /opt/scc/cscc --config
```
- Hit Enter to Acknowledge the change log
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

### Findings Summary

![STIG-Hardened](https://i.imgur.com/BsQNMcw.png)

Opening the non-compliance html results we can determine::
- Summary bullet point
- summary thoughts of analysis
- summary predictions

## Apply STIG Hardening Ansible Automation

We'll use the ansible stig role for rhel9 to remediate the non-compliant tests

Run:
```bash
ansible-playbook harden.yaml -vv
```
  <details close>
  <summary> <h4>Playbook Breakdown</h4> </summary>
    
  - Install collections from requirements file
  - Generate root SSH keypair

  </details>

## Perform Post-Hardening Validation

Run:
```bash
./scan.sh
```
  <details close>
  <summary> <h4>Script Breakdown</h4> </summary>
    
  - Install collections from requirements file
  - Generate root SSH keypair

  </details>

### Findings Summary

![STIG-Hardened](https://i.imgur.com/BsQNMcw.png)

Results from our scan post-hardened:
- Summary bullet point
- summary thoughts of analysis
- summary predictions

## Further Hardening with Custom Ansible Playbook

Run `custom.yaml` playbook to further harden the system:
```bash
ansible-playbook custom.yaml -vv
```
  <details close>
  <summary> <h4>Playbook Breakdown</h4> </summary>
    
  - Install collections from requirements file
  - Generate root SSH keypair

  </details>

## Final Validation

Run:
```bash
./scan.sh
```
### Findings Summary

![STIG-Hardened](https://i.imgur.com/BsQNMcw.png)

Results from our third scan:
- Summary bullet point
- summary thoughts of analysis
- summary predictions

## Conclusion

- ✅ Post-STIG Compliance Report (before/after results comparison)
- ✅ STIG Compliance Summary – Demonstrating compliance improvement.
