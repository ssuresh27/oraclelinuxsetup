# Oracle DB installation on RHEL 8 and 9 servers
README FILE: RHEL 8 /9 Linux setup for Oracle Software Installation

##### Oracle readiness for Oracle Database software install 
 
This Ansible Playbook can help you to to setup necessary configuration for installating Oracle Database software into the Linux environemnt. Please make sure you modify the necessary variables as per your own setup. Always play/test into lower setup multiple times before implementing into the actual live system.

###### Pre-requisites: 

The ansible play requires ssh connection between control node and target host. And, sudo access is required for oracle user. Following step can be performed to setup these pre-requisites.

1. Generate the SSH key pair, accept the default file name and leave the passphrase empty:
```
ssh-keygen -t rsa  (This is required for first time on ansible control/source node)
```
2. Copy the public key to target server(s), at prompt enter the target server login to copy the keys:
```
ssh-copy-id oracle@HOST_NAME
```
3. Verify the ssh connection working as expected, this step should give the date without asking password:
```
ssh oracle@HOST_NAME date
```
4. Provide sudo command access to oracle user in /etc/sudoers. As root user
```
visudo or vi /etc/sudoers
oracle ALL=NOPASSWD: sudo
```
5. Add oracle user into wheel group and grant NOPASSWORD access to wheel group. Save and close /etc/sudoers
```
## Same thing without a password
%wheel ALL=(ALL) NOPASSWD: ALL
```
6. Add oracle user into wheel group
```
usermod --append -G wheel oracle
````
Or add below in /etc/sudoers to provide direct access to oracle user
```
oracle ALL=(ALL) NOPASSWD: ALL
```

###### Referrence on setting SUDO access
[RHEL Note](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/managing-sudo-access_configuring-basic-system-settings)
[Oracle Note](https://docs.oracle.com/en/operating-systems/oracle-linux/8/userauth/userauth-GrantingsudoAccesstoUsers.html#topic_k3k_qkl_jvb)


# Summary Steps with this Ansible playbook are as below : 

** Script executed as oracle user with SUDO access **

0. Run Pre-checks oracle UID, Group ID and ORACLE_HOME is arleady exists
1. Write all the default ansible facts to localhost (control node) - default behaviour
2. The scripts are executed using oracle user and executes using SUDO access
3. Set kernel parameters as per Oracle recommendation (shmmax 80 % of RAM)
4. Install required operating system packages (Package list based on OS major version)
5. Set up shell limit for oracle user
6. Update /etc/hosts file entry with IP FQDN HOSTNAME
7. Create required directories
8. Setup MAILX using Postfix for RHEL8 & 9


### Setup:
 * OS: RHEL 8 and 9
 * Ansible: ansible 2.16.5
 * Database Version: Oracle 19 Linux 64bit

## Master Playbook:
linux_oracle.yml

role with this playbook: 

roles                  | tasks
---------------------- | --------------------------------------------------------
linux_oracle.yml       |  **settings for installing Oracle Database**

```
$ cat linux_oracle.yml
- hosts: all
  vars:
    oracle_base: /orabin/oracle
    oracle_home: /orabin/oracle/product/19.0.0.0
    oracle_gold_image: /oradba/software/19.0.0/db_home_192200_Linux-x86-64_JAN2024.zip

  roles:
    - linuxfororacle_prep
```

Inventory File

```
cat /home/oracle/dbadevn01.inv
[dbahost]
w2dbaodbdevn01

```
## Tree structure of this playbook
```
$ tree oraclelinuxsetup/
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
        │   ├── Step7_create_required_dirs.yml
        │   └── Step8_mailx.yml
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
*  --check will run the play without acutally executing
*  --diff will report difference between current and future state after ansible play
```
$ansible-playbook -i /home/oracle/dbadevn01.inv linux_oracle.yml --check 
$ansible-playbook -i /home/oracle/dbadevn01.inv linux_oracle.yml --check  --diff
```
 ### To run the play book

```
$ansible-playbook -i /home/oracle/dbadevn01.inv linux_oracle.yml -v
```
Note: Modify variables based on you setup or your requirements. 

###### Sample Run:

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
```
- NOTE
```
```