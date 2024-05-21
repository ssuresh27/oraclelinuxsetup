# Oracle 19C Post DB built tasks
## Perform post DB built tasks, run custom scripts, enable archivelog, hugepages and auto start

Note: Please modify all necessary configuration files based on your own environment.

### Setup:
 * OS: RHEL 8 and 9
 * Ansible: ansible 2.16.5
 * Database Version: Oracle RDBMS Software Version: 19.22 (RHEL 9 minimum required version is 19.22)


###  Summary Steps with this Ansible role as below: 
 * 1  Verify the DB/pmon running
 * 2  Enable ARCHIVELOG mode, run envrionement specific scripts
 * 3  Set Hugepage based on SGA
 * 4  Enable auto start for DB services
 * 5  Schedule RMAN/export backup jobs in cron


#### Summary commands: 

1. Default parameters (below values are set if no sepcified in <custom_playbook>.yml):

###### DB sepcification

> db_uniquename -> First two chars in upper case

db_uniquename: "{{ ansible_hostname[:2]|upper }}_{{ db_name }}" 
HUGE_PAGE: "{{ (DB_SGA|int * 1024 / 2 + 1) |int }}"

###### DB scripts

Script                          | Description
------------------------------- | ----------------------
standards_creation.sql          | Create standard profile and users for monitoring
update_unique_name.sql          | Change DB Unique Name
blockchange_tracking.sql        | Enable Block change tracking
enable_dbarchivelogs.sql        | Enable Archive log mode


###### cron jobs
```
#Daily RMAN Incremental Merge copy Database backup
30 20 * * * /oradba/backup/rman_db_incremental_merge_copy.sh {{ db_name }} {{ backup_dir }}  > /tmp/rman_db_incremental_{{ db_name }}.log  2>&1

#### Full export dump
00 04 * * 6 /oradba/backup/expdp_db.sh {{ db_name }} > /tmp/expdp_db_{{ db_name }}.log  2>&1

#Archive log backup for every 4 hours
0 */4 * * * /oradba/backup/rman_archivelogs.sh {{ db_name }} {{ backup_dir }} > /tmp/RMAN_archivelog_{{ db_name }}.log 2>&1

#Clean Orabin directory
0 0 * * * /oradba/maint/jaz/cleanhouse01.sh -d 15 > /tmp/cleanhouse.log 2>&1
```

2. Define variables as per your setup or requirements [ Modify <custom_playbook>.yml file]

3. Configure an Ansible inventory file (example as below) 
```
[root@rhel9 ansible]# cat ansible.cfg | grep inventory

```
4. Run the playbook role "Post_DB_built"
```
ansible-playbook Post_DB_built.yml [ with options for testing, use --check / --diff / --step / -vvv ]
```

You can use:
Mode         | Option for
------------ | -------------
--check      | Check mode is just a simulation
--diff       | reports the changes made
--step       | ansible to stop on each task, and ask if it should execute that task.
-v           | verbose mode (-vvv for more, -vvvv to enable connection debugging)

[Ansible Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)
