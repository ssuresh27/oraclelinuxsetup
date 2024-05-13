# oraclelinuxsetup
README FILE: RHEL 8 /9 Linux setup for Oracle Software Installation

Oracle readiness for Oracle Database software install 
 
This Ansible Playbook can help you to to setup necessary configuration for installating Oracle Database software into the Linux environemnt.
Please make sure you modify the necessary variables as per your own setup. Always play/test into lower setup multiple times before implementing into the 
actual live system.

```
Summary Steps with this Ansible playbook are as below : 

** Script executed as oracle user with SUDO access **

0. Run Pre-checks oracle UID, Group ID and ORACLE_HOME is arleady exists
1. Write all the default ansible facts to localhost (control node) - default behaviour
2. The scripts are executed using oracle user and executes using SUDO access
3. Set kernel parameters as per Oracle recommendation (shmmax 80 % of RAM)
4. Install required operating system packages (Package list based on OS major version)
5. Set up shell limit for oracle user
7. Update /etc/hosts file entry with IP FQDN HOSTNAME
8. Create required directories
```

Tree Structure for this playbook is as below - 

### Setup:
 * OS: RHEL 8 and 9
 * Ansible: ansible 2.7.6
 * Database Version: Oracle 19 Linux 64bit

## Master Playbook:
linux_oracle.yml

role with this playbook: 

roles                  | tasks
---------------------- | --------------------------------------------------------
linux_oracle.yml       |  **settings for installing Oracle Database**

```
[root@oel75 ansible]# cat linux_oracle.yml
- hosts: opsdev
  user: root

  roles:
   - linuxfororacle_prep
```
## Tree structure of this playbook
```
 [oracle@w2dbaodbdevn01 ~]$ tree oraclelinuxsetup/
oraclelinuxsetup/
├── facts
│   ├── w2dbaodbdevn01.na.gilead.com
│   └── w2dbaodbdevn01.na.gilead.com.3347621.2024-04-27@21:10:29~
├── linux_oracle.yml
├── README.md
└── roles
    └── linuxfororacle_prep
        ├── tasks
        │   ├── main.yml
        │   ├── oracle_bash_profile.yml
        │   ├── README.md
        │   ├── Step0_pre-check.yml
        │   ├── Step1_facts_to_file.yml
        │   ├── Step2_SUDO.yml
        │   ├── Step3_oracle_kernelparams.yml
        │   ├── Step4_oracle_packages.yml
        │   ├── Step5_oracle_users_shell_limit.yml
        │   ├── Step6_oracle_host_fileupdate.yml
        │   └── Step7_create_required_dirs.yml
        ├── templates
        └── vars
            ├── main.yml
            ├── RedHat8.yml
            └── RedHat9.yml

```

## Summary commands: 

Configure an Ansible inventory file (example as below) 
```
[oracle@w2dbaodbdevn01 ~]$ ansible-config dump|grep CONFIG_FILE
CONFIG_FILE() = None

[oracle@w2dbaodbdevn01 ~]$ cat dbadevn01.inv
[dbahost]
w2dbaodbdevn01
```
Note: Modify variables based on you setup or your requirements. 

Run the playbook role "cdb_pdb_create.yml"
```
ansible-playbook cdb_pdb_create.yml  

ansible-playbook linux_oracle.yml --check 
ansible-playbook linux_oracle.yml --check --diff

Sample Run:
```
[oracle@w2dbaodbdevn01 oraclelinuxsetup]$ ansible-playbook -i /home/oracle/dbadevn01.inv linux_oracle.yml --check --diff

PLAY [all] *************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************************
ok: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : Display pre installation of setting up Oracle Database Software requirements for RHEL 8 and later] *****************************************************************************************
ok: [w2dbaodbdevn01] => {
    "msg": [
        "This playbook is for setting setting up Oracle Database Software requirement 2024-05-13T23:05:49Z:"
    ]
}

TASK [linuxfororacle_prep : Check if Directory already exists] *********************************************************************************************************************************************************
ok: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : Oracle Home status] ************************************************************************************************************************************************************************
ok: [w2dbaodbdevn01] => {
    "msg": "/orabin/oracle/product/19.0.0/dbhome1 {'changed': False, 'stat': {'exists': False}, 'failed': False}"
}

TASK [linuxfororacle_prep : ORACLE_HOME Directry already exists] *******************************************************************************************************************************************************
skipping: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : Check if oracle user is exists with group id] **********************************************************************************************************************************************
ok: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : Oracle OS user status] *********************************************************************************************************************************************************************
skipping: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : Check if oradba group  is exists with group id] ********************************************************************************************************************************************
ok: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : oradba group status] ***********************************************************************************************************************************************************************
skipping: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : Check if oracle software is accessible] ****************************************************************************************************************************************************
ok: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : Error Oracle Home image/software is not available] *****************************************************************************************************************************************
skipping: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : save all facts to host specific file] ******************************************************************************************************************************************************
--- before: /home/oracle/oraclelinuxsetup/facts/w2dbaodbdevn01.na.gilead.com
+++ after: /home/oracle/.ansible/tmp/ansible-local-280733nqbkymmu/tmp8dhtq6kg
.
.
changed: [w2dbaodbdevn01 -> localhost]

TASK [linuxfororacle_prep : Check SUDO access] *************************************************************************************************************************************************************************
skipping: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : assert] ************************************************************************************************************************************************************************************
skipping: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : setup necessary kernel parameters] *********************************************************************************************************************************************************
ok: [w2dbaodbdevn01] => (item={'name': 'fs.file-max', 'value': 6815744})
ok: [w2dbaodbdevn01] => (item={'name': 'kernel.sem', 'value': '250 32000 100 128'})
ok: [w2dbaodbdevn01] => (item={'name': 'kernel.shmmni', 'value': 4096})
ok: [w2dbaodbdevn01] => (item={'name': 'kernel.shmall', 'value': 1073741824})
ok: [w2dbaodbdevn01] => (item={'name': 'kernel.shmmax', 'value': '6188276121'})
ok: [w2dbaodbdevn01] => (item={'name': 'kernel.panic_on_oops', 'value': 1})
ok: [w2dbaodbdevn01] => (item={'name': 'net.core.rmem_default', 'value': 262144})
ok: [w2dbaodbdevn01] => (item={'name': 'net.core.rmem_max', 'value': 4194304})
ok: [w2dbaodbdevn01] => (item={'name': 'net.core.wmem_default', 'value': 262144})
ok: [w2dbaodbdevn01] => (item={'name': 'net.core.wmem_max', 'value': 1048576})
ok: [w2dbaodbdevn01] => (item={'name': 'net.ipv4.conf.all.rp_filter', 'value': 2})
ok: [w2dbaodbdevn01] => (item={'name': 'net.ipv4.conf.default.rp_filter', 'value': 2})
ok: [w2dbaodbdevn01] => (item={'name': 'fs.aio-max-nr', 'value': 1048576})
ok: [w2dbaodbdevn01] => (item={'name': 'net.ipv4.ip_local_port_range', 'value': '9000 65500'})
ok: [w2dbaodbdevn01] => (item={'name': 'vm.hugetlb_shm_group', 'value': 503})

TASK [linuxfororacle_prep : setup necessary Packages] ******************************************************************************************************************************************************************
ok: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : install package] ***************************************************************************************************************************************************************************
ok: [w2dbaodbdevn01] => (item=bc)
ok: [w2dbaodbdevn01] => (item=binutils)
ok: [w2dbaodbdevn01] => (item=compat-openssl11)
ok: [w2dbaodbdevn01] => (item=elfutils-libelf)
ok: [w2dbaodbdevn01] => (item=fontconfig)
ok: [w2dbaodbdevn01] => (item=glibc)
ok: [w2dbaodbdevn01] => (item=glibc-devel)
ok: [w2dbaodbdevn01] => (item=ksh)
ok: [w2dbaodbdevn01] => (item=libaio)
ok: [w2dbaodbdevn01] => (item=libasan)
ok: [w2dbaodbdevn01] => (item=liblsan)
ok: [w2dbaodbdevn01] => (item=libX11)
ok: [w2dbaodbdevn01] => (item=libXau)
ok: [w2dbaodbdevn01] => (item=libXi)
ok: [w2dbaodbdevn01] => (item=libXrender)
ok: [w2dbaodbdevn01] => (item=libXtst)
ok: [w2dbaodbdevn01] => (item=libxcrypt-compat)
ok: [w2dbaodbdevn01] => (item=libgcc)
ok: [w2dbaodbdevn01] => (item=libibverbs)
ok: [w2dbaodbdevn01] => (item=libnsl)
ok: [w2dbaodbdevn01] => (item=librdmacm)
ok: [w2dbaodbdevn01] => (item=libstdc++)
ok: [w2dbaodbdevn01] => (item=libxcb)
ok: [w2dbaodbdevn01] => (item=libvirt-libs)
ok: [w2dbaodbdevn01] => (item=make)
ok: [w2dbaodbdevn01] => (item=policycoreutils)
ok: [w2dbaodbdevn01] => (item=policycoreutils-python-utils)
ok: [w2dbaodbdevn01] => (item=smartmontools)
ok: [w2dbaodbdevn01] => (item=sysstat)
ok: [w2dbaodbdevn01] => (item=mutt)
ok: [w2dbaodbdevn01] => (item=unzip)

TASK [linuxfororacle_prep : Add oracle user limits] ********************************************************************************************************************************************************************
ok: [w2dbaodbdevn01] => (item={'limit': 'soft', 'type': 'nofile', 'value': 4096})
ok: [w2dbaodbdevn01] => (item={'limit': 'hard', 'type': 'nofile', 'value': 65536})
ok: [w2dbaodbdevn01] => (item={'limit': 'soft', 'type': 'nproc', 'value': 2047})
ok: [w2dbaodbdevn01] => (item={'limit': 'hard', 'type': 'nproc', 'value': 16384})
ok: [w2dbaodbdevn01] => (item={'limit': 'soft', 'type': 'stack', 'value': 10240})
ok: [w2dbaodbdevn01] => (item={'limit': 'hard', 'type': 'stack', 'value': 32768})
ok: [w2dbaodbdevn01] => (item={'limit': 'soft', 'type': 'memlock', 'value': '6798643'})
ok: [w2dbaodbdevn01] => (item={'limit': 'hard', 'type': 'memlock', 'value': '6043238'})

TASK [linuxfororacle_prep : Update the /etc/hosts file with node name] *************************************************************************************************************************************************
ok: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : Check if Directory already exists] *********************************************************************************************************************************************************
ok: [w2dbaodbdevn01]

TASK [linuxfororacle_prep : create required directories] ***************************************************************************************************************************************************************
ok: [w2dbaodbdevn01] => (item=/orabin/oracle)
--- before
+++ after
@@ -1,4 +1,4 @@
 {
     "path": "/orabin/oracle/product/19.0.0/dbhome1",
-    "state": "absent"
+    "state": "directory"
 }

changed: [w2dbaodbdevn01] => (item=/orabin/oracle/product/19.0.0/dbhome1)

PLAY RECAP *************************************************************************************************************************************************************************************************************
w2dbaodbdevn01             : ok=15   changed=2    unreachable=0    failed=0    skipped=6    rescued=0    ignored=0

```diff
- NOTE
! Please do modify based on your own setup. This is purely based on my own lab setup. You can ask me any questions in relate to these playbooks - if you fork and modify to merge - let me know.
```
