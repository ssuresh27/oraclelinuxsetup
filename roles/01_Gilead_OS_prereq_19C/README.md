# Oracle DB installation on RHEL 8 and 9 servers
## Oracle readiness for Oracle Database software install
 
Note: Please modify all necessary configuration files based on your own environment.


### Setup:
 * OS: RHEL 8 and 9
 * Ansible: ansible 2.16.5
 * Database Version: Oracle RDBMS Software Version: 19.22 (RHEL 9 minimum required version is 19.22)
 * RHEL repo registration required to download rpms

###  Summary Steps with this Ansible role as below:

>The ansible play requires ssh connection between control node and target host. And, sudo access is required for oracle user. Following step can be performed to setup these pre-requisites.
[Steps to setup ssh & SUDO access](SUDOACCESS.md)

1. Run Pre-checks oracle UID, Group ID and ORACLE_HOME is arleady exists
2. Write all the default ansible facts to localhost (control node) - default behaviour
3. The scripts are executed using oracle user and executes using SUDO access
4. Set kernel parameters as per Oracle recommendation (shmmax 80 % of RAM)
5. Install required operating system packages (Package list based on OS major version)
6. Set up shell limit for oracle user
7. Update /etc/hosts file entry with IP FQDN HOSTNAME
8. Create required directories
9. Setup MAILX using Postfix for RHEL8 & 9


#### Summary commands: 

1. Default parameters (below values are set if no sepcified in <custom_playbook>.yml):

######  OS user properties
User        | uid    | group   |  gid
----------  | ------ | ------- | -------
oracle      | 500    | oradba  | 503

###### OS Kernel params
Parameter                   |   Value               |   Description
--------------------------- | --------------------- | ----------------------
fs.file-max                 |   6815744             | 
kernel.sem                  |   250 32000 100 128   |
kernel.shmmni               |   4096                |
kernel.shmall               |   1073741824          |
kernel.shmmax               |   mem_total_mb * .80  | 80 % of Total server memory
kernel.panic_on_oops        |   1                   |
net.core.rmem_default       |   262144              |
net.core.rmem_max           |   4194304             |
net.core.wmem_default       |   262144              |
net.core.wmem_max           |   1048576             |
fs.aio-max-nr               |   1048576             |
net.ipv4.ip_local_port_range|   9000 65500          |
vm.hugetlb_shm_group        |   503                 | For HugePages

###### oracle user OS Shell limits
Parameter                   |   Value               |   Description
--------------------------- | --------------------- | ----------------------
soft_no_file                |   4096                |
hard_no_file                |   65536               |
soft_nproc                  |   2047                |
hard_nproc                  |   16384               |
soft_stack                  |   10240               |
hard_stack                  |   32768               |
soft_memlock                | mem_total_mb * .90    | 90% of Total server memory
hard_memlock                | mem_total_mb * .90    | 90% of Total server memory

###### Packages list
> RHEL 8 
bc, binutils, elfutils-libelf, elfutils-libelf-devel, fontconfig-devel, glibc, glibc-devel, ksh, libaio, libaio-devel, libXrender, libX11, libXau, libXi, libXtst, libgcc, libnsl, librdmacm, libstdc++, libstdc++-devel, libxcb, libibverbs, make, policycoreutils, policycoreutils-python-utils, smartmontools, sysstatmutt, unzip, postfix

> RHEL 9
bc, binutils, compat-openssl11, elfutils-libelf, fontconfig, glibc, glibc-devel, ksh, libaio, libasan, liblsan, libX11, libXau, libXi, libXrender, libXtst, libxcrypt-compat, libgcc, libibverbs, libnsl, librdmacm, libstdc++, libxcb, libvirt-libs, make, policycoreutils, policycoreutils-python-utils, smartmontools, sysstat, mutt, unzip, postfix, s-nail 


2. Define variables as per your setup or requirements [ Modify <custom_playbook>.yml file]

3. Configure an Ansible inventory file (example as below) 
```
[root@rhel9 ansible]# cat ansible.cfg | grep inventory

```
4. Run the playbook role "OS_prereq_19C"
```
ansible-playbook OS_prereq_19C.yml [ with options for testing, use --check / --diff / --step / -vvv ]
```

You can use:
Mode         | Option for
------------ | -------------
--check      | Check mode is just a simulation
--diff       | reports the changes made
--step       | ansible to stop on each task, and ask if it should execute that task.
-v           | verbose mode (-vvv for more, -vvvv to enable connection debugging)

[Ansible Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)
