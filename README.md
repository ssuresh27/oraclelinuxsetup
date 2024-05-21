# Oracle DB installation on RHEL 8 and 9 servers
README FILE: RHEL 8 /9 Linux setup for Oracle Software Installation

##### Oracle readiness for Oracle Database software install 
 
This Ansible Playbook can help you to to setup necessary configuration for installating Oracle Database software into the Linux environemnt. Please make sure you modify the necessary variables as per your own setup. Always play/test into lower setup multiple times before implementing into the actual live system.

[SSH & SUDO access Pre-req](roles/01_OS_prereq_19C/SUDOACCESS.md)

[OS Pre-req](roles/01_OS_prereq_19C/README.md)

[Oracle 19C Binary Installation](roles/02_Install_dbsoftware_19C/README.md)

[Create Oracle 19C Non-CDB](roles/03_Create_db_19C/README.md)