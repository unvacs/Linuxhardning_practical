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

<sup>PCI-DSS:</sup><br>
<sup>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_grub2_password"><b>CCE-27309-4 (High)</b></a></sup>

##### Rationale

You can set password for the bootloader for prevents users from entering single user mode, changing settings at boot time, access to the bootloader console, reset the root password, if there is no password for GRUB-menu, access to non-secure operating systems and the ability to disable SELinux.

##### Generate password hash

```bash
# RedHat like distributions
grub2-mkpasswd-pbkdf2
grub2-setpassword # set it automatically

# Debian like distributions
grub-mkpasswd-pbkdf2
```

##### Update grub configuration

```bash
cat > /etc/grub.d/01_hash << __EOF__
set superusers="user"
password_pbkdf2 user
grub.pbkdf2.sha512.<hash> # rest of your password hash
__EOF__
```

##### Regenerate grub configuration

```bash
# RedHat like distributions
grub2-mkconfig > /boot/grub2/grub.cfg

# Debian like distributions
grub-mkconfig > /boot/grub/grub.cfg
```

##### Useful resources

- [Protecting Grub 2 With a Password](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-protecting_grub_2_with_a_password) <sup>Official RHEL Documentation</sup>
- [How To Password Protect GRUB Bootloader In Linux](https://www.ostechnix.com/password-protect-grub-bootloader-linux/)

#### Protect bootloader config files

<sup>PCI-DSS: <a href="https://static.open-scap.org/ssg-guides/ssg-centos7-guide-pci-dss.html#xccdf_org.ssgproject.content_rule_file_owner_grub2_cfg"><b>Medium</b></a> + <a href="https://static.open-scap.org/ssg-guides/ssg-centos7-guide-pci-dss.html#xccdf_org.ssgproject.content_rule_file_groupowner_grub2_cfg"><b>Medium</b></a></sup><br>
<sup>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_grub2_cfg"><b>CCE-27054-6 (Medium)</b></a> + <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_grub2_cfg"><b>CCE-26860-7 (Medium)</b></a></sup>

##### Rationale

To prevent local users from modifying the boot parameters  and ensure its configuration file's permissions are set properly.

##### Set the permissions on the bootloader config files

  > Bare-metal/VM task, not applicable for containers.

```bash
chmod 600 /boot/grub2/grub.cfg
chmod og-rwx /etc/grub.conf
chmod -R og-rwx /etc/grub.d
```

##### Set the owner and group of bootloader config files

  > Bare-metal/VM task, not applicable for containers.

Files/directories to **read** and **write** for root only:

```bash
chown root:root /etc/grub.conf
chown root:root /boot/grub2/grub.cfg
chown -R root:root /etc/grub.d
```

### Disk Partitioning

Critical file systems should be separated into different partitions in ways that make your system a better and more secure.

#### Separate disk partitions

<sup>PCI-DSS | <a href="">C2S/CIS (High)</a>, ID: </sup>

Make sure the following filesystems are mounted on separate partitions:

- `/boot`
- `/tmp`
- `/var`
- `/var/log`

Additionally, depending on the purpose of the server, you should consider separating the following partitions:

- `/usr`
- `/home`
- `/var/www`

You should also consider separating these partitions:

- `/var/tmp`
- `/var/log/audit`

###### Useful resources

- [Recommended partitioning scheme](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/installation_guide/s2-diskpartrecommend-x86)
- [Most secure way to partition linux?](https://security.stackexchange.com/questions/38793/most-secure-way-to-partition-linux)