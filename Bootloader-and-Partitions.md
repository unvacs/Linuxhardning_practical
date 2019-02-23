You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Bootloader](#bootloader)**
  * [Protect bootloader with password](#protect-bootloader-with-password)
  * [Protect bootloader config files](#protect-bootloader-config-files)

- **[Disk Partitioning](#disk-partitioning)**
  * [Separate disk partitions](#separate-disk-partitions)
  * [Restrict mount options](#restrict-mount-options)

## Bootloader

Protection for the bootloader can prevent unauthorized users who have physical access to the system, e.g. attaining root privileges through single user mode.

Basically when you want to prohibit unauthorized reconfiguring of your system, otherwise anybody could load and edit anything on it.

### Protect bootloader with password

#### Rationale

You can set password for the bootloader for prevents users from entering single user mode, changing settings at boot time, access to the bootloader console, reset the root password, access to non-secure operating systems and the ability to disable SELinux.

#### Solution

###### Generate password hash

```bash
# C2S/CIS: CCE-27309-4 (High)

grub2-mkpasswd-pbkdf2
grub2-setpassword # or set it automatically
```

###### Update grub configuration

```bash
# C2S/CIS: CCE-27309-4 (High)

cat > /etc/grub.d/01_hash << __EOF__
set superusers="user"
password_pbkdf2 user
grub.pbkdf2.sha512.<hash> # rest of your password hash
__EOF__
```

###### Regenerate grub configuration

```bash
# C2S/CIS: CCE-27309-4 (High)

grub2-mkconfig > /boot/grub2/grub.cfg
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_grub2_password">CCE-27309-4 (High)</a></code>

#### My comment

You should think about setting the password for bootloader because it can be problematic for the production clusters.

#### Useful resources

- [Protecting Grub 2 With a Password](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-protecting_grub_2_with_a_password) <sup>[Official]</sup>
- [How To Password Protect GRUB Bootloader In Linux](https://www.ostechnix.com/password-protect-grub-bootloader-linux/)

### Protect bootloader config files

#### Rationale

To prevent local users from modifying the boot parameters and ensure its configuration file's permissions are set properly.

#### Solution

###### Set the permissions on the bootloader config files

```bash
# C2S/CIS: CCE-27054-6 (Medium)

chmod 600 /boot/grub2/grub.cfg
chmod og-rwx /etc/grub.conf
chmod -R og-rwx /etc/grub.d
```

###### Set the owner and group on the bootloader config files

```bash
# C2S/CIS: CCE-26860-7 (Medium)

chown root:root /boot/grub2/grub.cfg
chown root:root /etc/grub.
chown -R root:root /etc/grub.d
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_grub2_cfg">CCE-27054-6 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_grub2_cfg">CCE-26860-7 (Medium)</a></code>

#### My comment

These rules are intended for Bare-metal/VM, not applicable for containers.

## Disk Partitioning

Critical file systems should be separated into different partitions in ways that make your system a better and more secure.

### Separate disk partitions

#### Rationale

There are several advantages of having partitions on your hard drive:

- prevents partitions overflow
- isolating data corruption
- logical separation of data
- duration of `fsck`
- using different file systems

#### Solution

C2S/CIS recommends that should be the following filesystems are mounted on a separate partitions:

- `/home`
- `/var/tmp`
- `/var`
- `/tmp`
- `/var/log/audit`
- `/var/log`

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_home">CCE-80144-9 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var_tmp">Unknown (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var">CCE-26404-4 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_tmp">CCE-27173-4 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var_log_audit">CCE-26971-2 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var_log">CCE-26967-0 (Low)</a></code>

#### My comment

I think you should also consider separating the following partitions (of course depending on the purpose of the server):

- `/usr`
- `/var/www`
- `/var/lib/pgsql` + `pg_log`

#### Useful resources

- [Recommended partitioning scheme](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/installation_guide/s2-diskpartrecommend-x86) <sup>[Official]</sup>
- [Most secure way to partition linux?](https://security.stackexchange.com/questions/38793/most-secure-way-to-partition-linux)

### Restrict mount options

#### Rationale

By default mount options are not focused on security. These options can be used to make certain types of malicious behavior more difficult.

#### Solution

###### /dev/shm

```bash
# C2S/CIS: CCE-80153-0 (unknown), CCE-80154-8 (unknown), CCE-80152-2 (unknown)

tmpfs  /dev/shm  tmpfs  rw,nodev,nosuid,noexec 0 0
```

###### /tmp

```bash
# C2S/CIS: CCE-80149-8 (unknown), CCE-80150-6 (unknown), CCE-80151-4 (unknown)

UUID=<...>  /tmp  ext4  defaults,nodev,nosuid,noexec  1 2
```

###### /var/tmp

```bash
# C2S/CIS: (unknown), (unknown), (unknown)

UUID=<...>  /var/tmp  ext4  defaults,nodev,nosuid,noexec  1 2
```

###### /home

```bash
# C2S/CIS: (unknown)

UUID=<...>  /home  ext4  defaults,nodev  1 2
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_dev_shm_noexec">CCE-80153-0 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_dev_shm_nosuid">CCE-80154-8 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_dev_shm_nodev">CCE-80152-2 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_tmp_nodev">CCE-80149-8 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_tmp_noexec">CCE-80150-6 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_tmp_nosuid">CCE-80151-4 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_var_tmp_nodev">(unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_var_tmp_noexec">(unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_var_tmp_nosuid">(unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_home_nodev">(unknown)</code>

#### My comment



#### Useful resources

- [Linux Security: Mount /tmp With nodev, nosuid, and noexec Options](https://www.cyberciti.biz/faq/linux-add-nodev-nosuid-noexec-options-to-temporary-storage-partitions/)
- [Security Handbook/Mounting partitions](https://wiki.gentoo.org/wiki/Security_Handbook/Mounting_partitions)
- [Increasing Linux server security with nodev, nosuid and no exec options](https://kb.iweb.com/hc/en-us/articles/230267488--Increasing-Linux-server-security-with-nodev-nosuid-and-no-exec-options)
- [Why it is important to Securing /dev/shm and /tmp](https://askubuntu.com/questions/389408/why-it-is-important-to-securing-dev-shm-and-tmp)
- [Securing /dev/shm partition](https://www.gnutoolbox.com/securing-devshm-partition/)
- [Linux system hardening: adding hidepid to /proc mount point](https://linux-audit.com/linux-system-hardening-adding-hidepid-to-proc/)
- [dm-crypt/Swap encryption](https://wiki.archlinux.org/index.php/Dm-crypt/Swap_encryption)
- [Encrypted swap partition on Debian/Ubuntu](https://feeding.cloud.geek.nz/posts/encrypted-swap-partition-on/)
