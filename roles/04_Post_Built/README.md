# Oracle_19C_Non-CDB
## Create Oracle Non-Container database

Note: Please modify all necessary configuration files based on your own environment.

### Setup:
 * OS: RHEL 8 and 9
 * Ansible: ansible 2.16.5
 * Database Version: Oracle RDBMS Software Version: 19.22 (RHEL 9 minimum required version is 19.22)


###  Summary Steps with this Ansible role as below: 
 * 1  Verify the DB is already configured
 * 2  Create and configure DB listener
 * 3  Create NON-CDB database response file using input/default parameters in NOARCHIVELOG MODE
 * 4  Create a NON-CDB in silent mode using response file
 * 5  Update TNS & bash profile


#### Summary commands: 

1. Default parameters (below values are set if no sepcified in <custom_playbook>.yml):

###### DB sepcification
db_name: ORCL
db_port: 1521

###### DB Character sets
characterSet: AL32UTF8
nationalCharacterSet: AL16UTF16

#Block_size
block_size: 8192

###### DB SGA 50%  & PGA 10% in GB
DB_SGA: "{{ (ansible_memtotal_mb  * 0.50 / 1024 ) |round | int }}"
DB_PGA: "{{ (ansible_memtotal_mb  * 0.10 / 1024 ) |round | int }}"

###### DB templates
template_name: Gilead_Minimum_19.0.0.0

template_name                   |   Provided        |   Description
------------------------------- |   --------------- |  ---------------
General_Purpose.dbc             |   Oracle          |   Includes all the default options
Data_Warehouse.dbc              |   Oracle          |   For DW DB with custom block size
Gilead_Minimum_19.0.0.0.dbt     |   Custom          |   With Minimum components (default)
Gilead_Oracle_Text_19.0.0.0.dbt |   Custom          |   Mimimum + Oracle text

###### DB Directory
Name              |  Value
------------------|-----------------------
oracle_base       |  /orabin/oracle
oracle_home       |  /orabin/oracle/product/19.0.0
oracle_inventory  |  /orabin/oraInventory
stage_dir         |  /home/oracle/stage
data_dir          |  /oradata/{{ db_name }} (DB FILES)
arch_dir          |  /oradata/{{ db_name }}/arch
backup_dir        |  /orabkup/{{ db_name }}
tns_admin         |  /{{oracle_home }}/network/admin


2. Define variables as per your setup or requirements [ Modify <custom_playbook>.yml file]

3. Configure an Ansible inventory file (example as below) 
```
[root@rhel9 ansible]# cat ansible.cfg | grep inventory

```
4. Run the playbook role "Oracle_19C_DB"
```
ansible-playbook Oracle_19C_DB.yml [ with options for testing, use --check / --diff / --step / -vvv ]
```

You can use:
Mode         | Option for
------------ | -------------
--check      | Check mode is just a simulation
--diff       | reports the changes made
--step       | ansible to stop on each task, and ask if it should execute that task.
-v           | verbose mode (-vvv for more, -vvvv to enable connection debugging)

[Ansible Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)
