You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

### Table of Contents

- **[Bootloader](#bootloader)**
  * [Protect bootloader with password](#protect-bootloader-with-password)
  * [Protect bootloader config files](#protect-bootloader-config-files)

- **[Disk Partitioning](#disk-partitioning)**
  * [Separate disk partitions](#separate-disk-partitions)
  * [Restrict mount options](#restrict-mount-options)

### Bootloader

Protection for the bootloader can prevent unauthorized users who have physical access to the system, e.g. attaining root privileges through single user mode.

Basically when you want to prohibit unauthorized reconfiguring of your system, otherwise anybody could load and edit anything on it.

#### Protect bootloader with password

##### Rationale

You can set password for the bootloader for prevents users from entering single user mode, changing settings at boot time, access to the bootloader console, reset the root password, access to non-secure operating systems and the ability to disable SELinux.

##### Solution

###### Generate password hash

```bash
# RedHat like distributions
grub2-mkpasswd-pbkdf2
grub2-setpassword # set it automatically

# Debian like distributions
grub-mkpasswd-pbkdf2
```

###### Update grub configuration

```bash
cat > /etc/grub.d/01_hash << __EOF__
set superusers="user"
password_pbkdf2 user
grub.pbkdf2.sha512.<hash> # rest of your password hash
__EOF__
```

###### Regenerate grub configuration

```bash
# RedHat like distributions
grub2-mkconfig > /boot/grub2/grub.cfg

# Debian like distributions
grub-mkconfig > /boot/grub/grub.cfg
```

<sup>PCI-DSS: <b>doesn't exist</b></sup><br>
<sup>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_grub2_password"><b>CCE-27309-4 (H)</b></a></sup>

##### My comment

You should think about setting the password for bootloader because it can be problematic for production servers.

##### Useful resources

- [Protecting Grub 2 With a Password](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-protecting_grub_2_with_a_password) <sup>[Official]</sup>
- [How To Password Protect GRUB Bootloader In Linux](https://www.ostechnix.com/password-protect-grub-bootloader-linux/)

#### Protect bootloader config files

##### Rationale

To prevent local users from modifying the boot parameters and ensure its configuration file's permissions are set properly.

##### Solution

###### Set the permissions on the bootloader config files

  > Bare-metal/VM task, not applicable for containers.

```bash
chmod 600 /boot/grub2/grub.cfg
chmod og-rwx /etc/grub.conf
chmod -R og-rwx /etc/grub.d
```

<sup>PCI-DSS: <a href="https://static.open-scap.org/ssg-guides/ssg-centos7-guide-pci-dss.html#xccdf_org.ssgproject.content_rule_file_owner_grub2_cfg"><b>Unknown (M)</b></a></sup><br>
<sup>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_grub2_cfg"><b>CCE-27054-6 (M)</b></a></sup>

###### Set the owner and group on the bootloader config files

  > Bare-metal/VM task, not applicable for containers.

Files/directories to **read** and **write** for root only to prevent destruction or modification:

```bash
chown root:root /etc/grub.conf
chown root:root /boot/grub2/grub.cfg
chown -R root:root /etc/grub.d
```

<sup>PCI-DSS: <a href="https://static.open-scap.org/ssg-guides/ssg-centos7-guide-pci-dss.html#xccdf_org.ssgproject.content_rule_file_groupowner_grub2_cfg"><b>Unknown (M)</b></a></sup><br>
<sup>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_grub2_cfg"><b>CCE-26860-7 (M)</b></a></sup>

### Disk Partitioning

Critical file systems should be separated into different partitions in ways that make your system a better and more secure.

#### Separate disk partitions

##### Policies

<sup>PCI-DSS: <b>doesn't exist</b></sup><br>
<sup>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_home"><b>CCE-80144-9 (L)</b></a>, <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var_tmp"><b>Unknown (L)</b></a>, <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var"><b>CCE-26404-4 (L)</b></a>, <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_tmp"><b>CCE-27173-4 (L)</b></a>, <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var_log_audit"><b>CCE-26971-2 (L)</b></a>, <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var_log"><b>CCE-26967-0 (L)</b></a></sup>

##### Rationale

There are several advantages of having partitions on your hard drive:

- prevents partitions overflow
- isolating data corruption
- logical separation of data
- duration of `fsck`
- using different file systems

C2S/CIS recommends that should be the following filesystems are mounted on a separate partitions:

- `/home`
- `/var/tmp`
- `/var`
- `/tmp`
- `/var/log/audit`
- `/var/log`

I think you should consider separating the following partitions (depending on the purpose of the server):

- `/usr`
- `/var/www`
- `/var/lib/pgsql` + `pg_log`

##### Useful resources

- [Recommended partitioning scheme](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/installation_guide/s2-diskpartrecommend-x86) <sup>[Official]</sup>
- [Most secure way to partition linux?](https://security.stackexchange.com/questions/38793/most-secure-way-to-partition-linux)

#### Restrict mount options

By default mount options are not focused on security.

##### Rationale

##### Solution

PCI-DSS:
C2S/CIS:

###### Set the permissions on the bootloader config files

PCI-DSS:
C2S/CIS:

###### Set the owner and group of bootloader config files

##### Useful resources
