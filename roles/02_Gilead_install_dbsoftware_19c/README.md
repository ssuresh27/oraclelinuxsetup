# Oracle 19C Standalone Binary installation (Single Instances)
## Install 19C Binary

Note: Please modify all necessary configuration files based on your own environment.

Oracle Installation Prerequisites: Database Installation Guide for Linux 
(https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/index.html)

### Setup:
 * OS: REHL 8 and 9
 * Ansible: ansible 2.16.5
 * Database Version: Oracle 19.22 Linux64 (RHEL 9 minimum required version is 19.22)

Oracle RDBMS Software:
> The software is staged in shared NFS location when this can be accessed from target server(s).


### Summary Steps with this Ansible role as below:  
 * 1  Verify the shell limit 
 * 2  Check the software location has minimum of 20G free space
 * 3  Unpack Oracle 19c RDBMS Software
 * 4  Install Oracle 19c RDBMS Software
 * 5  Execute oraInstroot.sh script [Uses assume CV_ASSUME_DISTID per Oracle recommendation]
 * 6  Execute root.sh script
 * 7  Validation - Connect to SQLPLUS binary. 

#### Summary commands: 

1. Default parameters (below values  are set if no sepcified in <custom_playbook>.yml):

###### Oracle 19C Gold Image

oracle_gold_image: /oradba/software/19.0.0/db_home_192200_Linux-x86-64_JAN2024.zip

###### DB Directory
Name              |  Value
------------------|-----------------------
oracle_base       |  /orabin/oracle
oracle_home       |  /orabin/oracle/product/19.0.0
oracle_inventory  |  /orabin/oraInventory
stage_dir         |  /home/oracle/stage

2. Configure an Ansible inventory file (example as below) 

```
[root@rhel9 ansible]# cat ansible.cfg | grep inventory

```

3. Run the playbook role `install_dbsoftware_19c`
```
ansible-playbook install_dbsoftware_19c.yml  [ with options for testing, use --check / --diff / --step / -vvv ]
```

You can use:
Mode         | Option for
------------ | -------------
--check      | Check mode is just a simulation
--diff       | reports the changes made
--step       | ansible to stop on each task, and ask if it should execute that task.
-v           | verbose mode (-vvv for more, -vvvv to enable connection debugging)

[Ansible](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)
