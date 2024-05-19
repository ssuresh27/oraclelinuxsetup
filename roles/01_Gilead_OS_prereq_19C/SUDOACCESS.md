# Steps to setup SSH and SUDO access
#### SSH Access

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
#### SUDO Access
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

