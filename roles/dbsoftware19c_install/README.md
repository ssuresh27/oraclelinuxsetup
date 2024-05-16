# OracleDBAwithAnsible SI (Single Instances)

Note: Please modify all necessary configuration files based on your own environment.

This article describes the installation of Oracle Database 19c 64-bit on RHEL 8 and 9 64-bit.

Oracle Installation Prerequisites: Database Installation Guide for Linux 
(https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/index.html)

**Setup:**
 * OS: REHL 8 and 9
 * Ansible: ansible 2.16.5
 * Database Version: Oracle 19.22 Linux64 (19.22 is minimum required version for RHEL 9)

Oracle RDBMS Software:
> The software is staged in shared NFS location when this can be accessed from target server(s).

- Install Oracle Database Software
Oracle DBA - Automation with Ansible (Install Oracle 19c Database Software)

### Summary Steps:  
 * 1  Verify the shell limit 
 * 2  Check the software location has minimum of 20G free space
 * 3  Unpack Oracle 19c RDBMS Software
 * 4  Install Oracle 19c RDBMS Software
 * 5  Execute oraInstroot.sh script [Uses assume CV_ASSUME_DISTID per Oracle recommendation]
 * 6  Execute root.sh script
 * 7  Validation - Connect to SQLPLUS binary. 

### Summary commands: 

1. Clone this repository:
   git clone https://github.com/asiandevs/OracleDBAwithAnsible

2. Stage the following Oracle Software on the control machine
> Oracle Database 19c (19.3) for Linux x86-64 [ LINUX.X64_193000_db_home.zip ]
> https://www.oracle.com/technetwork/database/enterprise-edition/downloads/oracle19c-linux-5462157.html

3. Configure an Ansible inventory file (example as below) 

```
[root@oel75 ansible]# cat ansible.cfg | grep inventory
inventory = ./inventory
[root@oel75 ansible]# cat inventory
[ora-x1]
192.168.56.102
[ora-x2]
192.168.56.103
[dbservers]
192.168.56.102
192.168.56.103
```

4. Run the playbook role `dbsoftware19c_install`
```
ansible-playbook dbsoftware19c_install  [ with options for testing, use --check / --diff / --step / -vvv ]
```

You can use:
Mode         | Option for
------------ | -------------
--check      | Check mode is just a simulation
--diff       | reports the changes made
--step       | ansible to stop on each task, and ask if it should execute that task.
-v           | verbose mode (-vvv for more, -vvvv to enable connection debugging)

[Ansible](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)
