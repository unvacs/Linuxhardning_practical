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

<sup>PCI-DSS | <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_grub2_password">C2S/CIS (High)</a>, ID: CCE-27309-4</sup>

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

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-centos7-guide-pci-dss.html#xccdf_org.ssgproject.content_rule_file_owner_grub2_cfg">PCI-DSS (Medium)</a> + <a href="https://static.open-scap.org/ssg-guides/ssg-centos7-guide-pci-dss.html#xccdf_org.ssgproject.content_rule_file_groupowner_grub2_cfg">PCI-DSS (Medium)</a> | <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_grub2_cfg">C2S/CIS (Medium)</a>, ID: CCE-27054-6 + <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_grub2_cfg">C2S/CIS (Medium)</a>, ID: CCE-26860-7</sup>

##### Rationale

To prevent local users from modifying the boot parameters  and ensure its configuration file's permissions are set properly.

Set the owner and group of `/etc/grub.conf` to the root user:

```bash
chown root:root /etc/grub.conf
```

or

```bash
chown -R root:root /etc/grub.d
```

Set permission on the `/etc/grub.conf` or `/etc/grub.d` file to read and write for root only:

```bash
chmod og-rwx /etc/grub.conf
```

or

```bash
chmod -R og-rwx /etc/grub.d
```

### Partitions

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