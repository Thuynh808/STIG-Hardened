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
