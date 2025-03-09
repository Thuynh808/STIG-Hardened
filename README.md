# STIG-Hardened

Harden a **RHEL 9** for STIG compliance

https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_RHEL_9_V2R3_STIG.zip

https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_RHEL_9_V2R3_STIG_Ansible.zip

https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_STIGViewer-win32_x64-3-5-0.zip

![elastic_labs](https://i.imgur.com/BsQNMcw.png)

## Project Overview
The elastic_labs project is designed 

## Objectives
- Automate majority of the setup using Ansible, from system configuration to application deployment
- Integrate Elasticsearch and Kibana for data indexing and visualization

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

## Findings Summary

Lets view the results by opening the non compliance html file in a browser. 

![STIG-Hardened](https://i.imgur.com/BsQNMcws.png)

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
  
![STIG-Hardened](https://i.imgur.com/BsQNMcws.png)

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
  <details close>
  <summary> <h4>Script Breakdown</h4> </summary>
    
  - Install collections from requirements file
  - Generate root SSH keypair

  </details>
  
![STIG-Hardened](https://i.imgur.com/BsQNMcws.png)

- Summary bullet point
- summary thoughts of analysis
- summary predictions

## Conclusion

- ✅ Post-STIG Compliance Report (before/after results comparison)
- ✅ STIG Compliance Summary – Demonstrating compliance improvement.




General Workflow for STIG Hardening (Using SCC)

1. Understand the Scope & Compliance Requirements
	- Identify which operating systems (RHEL, Windows Server, Ubuntu) and applications (Apache, SQL Server) need STIG compliance.
	- Determine the STIG benchmark version (latest from DISA cyber.mil).
	- Clarify if we are targeting DoD STIG Level 1 (basic security) or Level 2 (higher security with more restrictions).

Deliverables:
- ✅ Project Plan/Scope Document – Defines systems, STIG baselines, deadlines.



2. Perform Initial STIG Compliance Assessment
	- Run SCAP Compliance Checker (SCC) for the initial compliance scan.
	- Generate STIG Viewer Checklist (.ckl) files for each system.
	- Identify major vulnerabilities and areas that need hardening.

Command Example:

sudo cscc --report /tmp/pre-stig-report.html

Deliverables:
- ✅ Initial Compliance Report (HTML, XML, or STIG .ckl file)
- ✅ Findings Summary – Executive summary of major gaps.



3. Apply STIG Hardening (Automated & Manual Fixes)
	- Automate Remediation using Ansible STIG role (if available for the OS).
	- Manually apply fixes for non-automatable rules (e.g., physical security policies, password complexity changes).
	- Update system audit configurations (e.g., auditd, rsyslog for Linux, Event Viewer for Windows).

Ansible Example for RHEL STIG Hardening:

ansible-galaxy install -r requirements.yml
ansible-playbook -i inventory stig-harden.yml

Deliverables:
- ✅ Remediation Playbooks (Ansible/Scripts) – To apply fixes.
- ✅ Manual Fixes Documentation – For non-automatable controls.



4. Perform Post-Hardening Validation
	- Re-run SCC to verify improvements.
	- Compare pre- and post-STIG compliance scores.
	- Generate final STIG Viewer .ckl files for each system.

Command Example:

sudo cscc --report /tmp/post-stig-report.html

Deliverables:
- ✅ Post-STIG Compliance Report (before/after results comparison)
- ✅ STIG Compliance Summary – Demonstrating compliance improvement.



5. Final Documentation & Handoff
	- Prepare STIG Compliance Checklist in .ckl format.
	- Provide System Security Plan (SSP) with details on:
	- Hardening steps taken.
	- Remaining exceptions (e.g., workarounds for software limitations).
	- Continuous monitoring plan.

Deliverables:
- ✅ Final STIG Compliance Report & Checklist (.ckl)
- ✅ Hardened System Images/Snapshots – To allow re-deployment if needed.
- ✅ Executive Summary for Management – High-level results and compliance status.



- What the Manager Expects from Us:
  - A structured and repeatable hardening process – Using SCC and Ansible where possible.
	 - A measurable improvement in compliance – Targeting 80%+ compliance.
	 - Clear documentation of changes – STIG checklist (.ckl), system hardening guides.
	 - Minimal disruption to operations – Proper testing before enforcing changes.
	 - A finalized report and compliance package – Ready for submission to security auditors.



