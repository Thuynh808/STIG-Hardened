# STIG-Hardened

Harden a **RHEL 9** for STIG compliance

insert dvd

on control, run 







https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_RHEL_9_V2R3_STIG.zip

https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_RHEL_9_V2R3_STIG_Ansible.zip

https://dl.dod.cyber.mil/wp-content/uploads/stigs/zip/U_STIGViewer-win32_x64-3-5-0.zip


---

## Perform initial scan

ssh ansible@`<rhel-stig-ip>`

sudo /opt/scc/cscc --config

Hit Enter to Acknowledge the change log

Hit 1 and Enter 1: Configure SCAP content.

Hit 1-8 and Enter to deselect all content

Hit 6 and Enter to select RHEL_9_STIG

Hit 0 and Enter to return to the main menu.

Hit 6 and enter Configuration Options.

Hit 1 and enter Scanning Options

Hit 1 and Enter to Turn on option 1, "Run all SCAP content regardless of applicability"

Hit 0 and Enter to return to the previous.

Hit 0 and Enter to return to the main menu.

Hit 9 and Enter to exit, save changes, and perform scan

---
Got it! Below is the STIG hardening workflow using SCAP Compliance Checker (SCC) instead of OpenSCAP.

⸻

General Workflow for STIG Hardening (Using SCC)

1. Understand the Scope & Compliance Requirements
	•	Identify which operating systems (RHEL, Windows Server, Ubuntu) and applications (Apache, SQL Server) need STIG compliance.
	•	Determine the STIG benchmark version (latest from DISA cyber.mil).
	•	Clarify if we are targeting DoD STIG Level 1 (basic security) or Level 2 (higher security with more restrictions).

Deliverables:
✅ Project Plan/Scope Document – Defines systems, STIG baselines, deadlines.

⸻

2. Perform Initial STIG Compliance Assessment
	•	Run SCAP Compliance Checker (SCC) for the initial compliance scan.
	•	Generate STIG Viewer Checklist (.ckl) files for each system.
	•	Identify major vulnerabilities and areas that need hardening.

Command Example:

sudo cscc --report /tmp/pre-stig-report.html

Deliverables:
✅ Initial Compliance Report (HTML, XML, or STIG .ckl file)
✅ Findings Summary – Executive summary of major gaps.

⸻

3. Apply STIG Hardening (Automated & Manual Fixes)
	•	Automate Remediation using Ansible STIG role (if available for the OS).
	•	Manually apply fixes for non-automatable rules (e.g., physical security policies, password complexity changes).
	•	Update system audit configurations (e.g., auditd, rsyslog for Linux, Event Viewer for Windows).

Ansible Example for RHEL STIG Hardening:

ansible-galaxy install -r requirements.yml
ansible-playbook -i inventory stig-harden.yml

Deliverables:
✅ Remediation Playbooks (Ansible/Scripts) – To apply fixes.
✅ Manual Fixes Documentation – For non-automatable controls.

⸻

4. Perform Post-Hardening Validation
	•	Re-run SCC to verify improvements.
	•	Compare pre- and post-STIG compliance scores.
	•	Generate final STIG Viewer .ckl files for each system.

Command Example:

sudo cscc --report /tmp/post-stig-report.html

Deliverables:
✅ Post-STIG Compliance Report (before/after results comparison)
✅ STIG Compliance Summary – Demonstrating compliance improvement.

⸻

5. Final Documentation & Handoff
	•	Prepare STIG Compliance Checklist in .ckl format.
	•	Provide System Security Plan (SSP) with details on:
	•	Hardening steps taken.
	•	Remaining exceptions (e.g., workarounds for software limitations).
	•	Continuous monitoring plan.

Deliverables:
✅ Final STIG Compliance Report & Checklist (.ckl)
✅ Hardened System Images/Snapshots – To allow re-deployment if needed.
✅ Executive Summary for Management – High-level results and compliance status.

⸻

What the Manager Expects from Us
	1.	A structured and repeatable hardening process – Using SCC and Ansible where possible.
	2.	A measurable improvement in compliance – Targeting 80%+ compliance.
	3.	Clear documentation of changes – STIG checklist (.ckl), system hardening guides.
	4.	Minimal disruption to operations – Proper testing before enforcing changes.
	5.	A finalized report and compliance package – Ready for submission to security auditors.

⸻

