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

### Protect bootloader with password

#### Rationale

Protection for the bootloader can prevent unauthorized users who have physical access to the system, e.g. attaining root privileges through single user mode.

You can set password for the bootloader for prevents users from entering single user mode, changing settings at boot time, access to the bootloader console, reset the root password, access to non-secure operating systems and the ability to disable SELinux.

#### Solution

###### Generate password hash

```bash
grub2-setpassword
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_grub2_password">C2S/CIS: CCE-27309-4 (High)</a></sup>

###### Update grub configuration

```bash
sed -i s/root/bootuser/g /etc/grub.d/01_users
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_grub2_password">C2S/CIS: CCE-27309-4 (High)</a></sup>

###### Regenerate grub configuration

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_grub2_password">C2S/CIS: CCE-27309-4 (High)</a></sup>

#### Comments

  > You should think about sense of setting the password for bootloader because it can be problematic for the production clusters.

Also other solution is:

###### Generate password hash

```bash
grub2-mkpasswd-pbkdf2
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
grub2-mkconfig > /boot/grub2/grub.cfg
```

#### Useful resources

- [Protecting Grub 2 With a Password](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-protecting_grub_2_with_a_password) <sup>[Official]</sup>
- [How To Password Protect GRUB Bootloader In Linux](https://www.ostechnix.com/password-protect-grub-bootloader-linux/)

### Protect bootloader config files

#### Rationale

To prevent local users from modifying the boot parameters and ensure its configuration file's permissions are set properly.

#### Solution

###### Set the permissions on the bootloader config files

```bash
chmod 600 /boot/grub2/grub.cfg
chmod og-rwx /etc/grub.conf
chmod -R og-rwx /etc/grub.d
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_grub2_cfg">C2S/CIS: CCE-27054-6 (Medium)</a></sup>

###### Set the owner and group on the bootloader config files

```bash
chown root:root /boot/grub2/grub.cfg
chown root:root /etc/grub.conf
chown -R root:root /etc/grub.d
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_grub2_cfg">C2S/CIS: CCE-26860-7 (Medium)</a></sup>

#### Comments

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
- using specific SELinux contexts

#### Solution

C2S/CIS recommends that should be the following filesystems are mounted on a separate partitions:

- `/home`
- `/var/tmp`
- `/var`
- `/tmp`
- `/var/log/audit`
- `/var/log`

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_home">C2S/CIS: CCE-80144-9 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var_tmp">C2S/CIS: Unknown (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var">C2S/CIS: CCE-26404-4 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_tmp">C2S/CIS: CCE-27173-4 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var_log_audit">C2S/CIS: CCE-26971-2 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_partition_for_var_log">C2S/CIS: CCE-26967-0 (Low)</a></sup>

#### Comments

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
tmpfs  /dev/shm  tmpfs  rw,nodev,nosuid,noexec 0 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_dev_shm_noexec">C2S/CIS: CCE-80153-0 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_dev_shm_nosuid">C2S/CIS: CCE-80154-8 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_dev_shm_nodev">C2S/CIS: CCE-80152-2 (Medium)</a></sup>

###### /tmp

```bash
UUID=<...>  /tmp  ext4  defaults,nodev,nosuid,noexec  1 2
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_tmp_nodev">C2S/CIS: CCE-80149-8 (Unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_tmp_noexec">C2S/CIS: CCE-80150-6 (Unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_tmp_nosuid">C2S/CIS: CCE-80151-4 (Unknown)</a></sup>

###### /var/tmp

```bash
UUID=<...>  /var/tmp  ext4  defaults,nodev,nosuid,noexec  1 2
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_var_tmp_nodev">C2S/CIS: No-CCE (Unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_var_tmp_noexec">C2S/CIS: No-CCE (Unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_var_tmp_nosuid">C2S/CIS: No-CCE (Unknown)</a></sup>

###### /home

```bash
UUID=<...>  /home  ext4  defaults,nodev  1 2
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_mount_option_home_nodev">C2S/CIS: No-CCE (Unknown)</a></sup>

#### Comments

##### Mount options: `nodev`, `nosuid` and `noexec`

First of all look at these options to understand how they works:

- `nodev` - specifies that the filesystem cannot contain special devices: This is a security precaution. You don't want a user world-accessible filesystem like this to have the potential for the creation of character devices or access to random device hardware

- `nosuid` - specifies that the filesystem cannot contain set userid files. Preventing setuid binaries on a world-writable filesystem makes sense because there's a risk of root escalation or other awfulness there

- `noexec` - this param might be useful for a partition that contains no binaries, like **/var**, or contains binaries you do not want to execute on your system (from partitions with `noexec`), or that cannot even be executed on your system

##### Secure /boot directory

In my opinion you should also secure the boot directory which contains important files related to the Linux kernel, so you need to make sure that this directory is locked down to read-only permissions.

Add **ro** option and `nodev`, `nosuid` and `noexec` to `/etc/fstab` for **/boot** entry:

```bash
LABEL=/boot  /boot  ext2  defaults,ro,nodev,nosuid,noexec  1 2
```

  > When updating the kernel you will have to move the flag to `rw`:
  > ```bash
  > mount -o remount,defaults,rw /boot
  > ```

##### Secure /tmp and /var/tmp

C2S/CIS profile also talking about it. For me this configuration can be even better.

On the Linux systems, the **/tmp** and **/var/tmp** locations are world-writable. Several daemons/applications use the **/tmp** or **/var/tmp** directories to temporarily store data, log information, or to share information between their sub-components. However, due to the shared nature of these directories, several attacks are possible, including:

- Leaks of confidential data via secrets in file names
- Race-condition attacks (TOCTOU) on the integrity of processes and data
- Denial-of-Service (DoS) attacks based on race conditions and pre-allocating file/directory names

As a rule of thumb, malicious applications usually write to **/tmp** and then attempt to run whatever was written. A way to prevent this is to mount **/tmp** on a separate partition with the options `nodev`, `nosuid` and `noexec` enabled.

This will deny binary execution from **/tmp**, disable any binary to be suid root, and disable any block devices from being created.

**The first possible scenario is create symlink**

```bash
mv /var/tmp /var/tmp.old
ln -s /tmp /var/tmp
cp -prf /var/tmp.old/* /tmp && rm -fr /var/tmp.old
```

and set properly mount params:

```bash
UUID=<...>  /tmp  ext4  defaults,nodev,nosuid,noexec  1 2
```

**The second scenario is a bind mount**

The storage location **/var/tmp** should be bind mounted to **/tmp**, as having multiple locations for temporary storage is not required:

```bash
/tmp  /var/tmp  none  rw,nodev,nosuid,noexec,bind  0 0
```

**The third scenario is setting up polyinstantiated directories**

Create new directories:

```bash
mkdir --mode 000 /tmp-inst
mkdir --mode 000 /var/tmp/tmp-inst
```

Edit `/etc/security/namespace.conf`:

```bash
 /tmp      /tmp-inst/          level  root,adm
 /var/tmp  /var/tmp/tmp-inst/  level  root,adm
```

Set correct **SELinux** context:

```bash
setsebool polyinstantiation_enabled=1
chcon --reference=/tmp /tmp-inst
chcon --reference=/var/tmp/ /var/tmp/tmp-inst
```

And set `nodev`, `nosuid` and `noexec` mount options in `/etc/fstab`.

  > Alternative for **polyinstantiated directories** is **PrivateTmp** feature available from **systemd**. For more information please see: [New Red Hat Enterprise Linux 7 Security Feature: PrivateTmp](https://access.redhat.com/blogs/766093/posts/1976243).

##### Secure /dev/shm

`/dev/shm` is a temporary file storage filesystem, i.e. **tmpfs**, that uses RAM for the backing store. One of the major security issue with the `/dev/shm` is anyone can upload and execute files inside the `/dev/shm` similar to the `/tmp` partition. Further the size should be limited to avoid an attacker filling up this mountpoint to the point where applications could be affected (normally it allows 20% or more of RAM to be used). The sticky bit should be set like for any world writeable directory.

For applies to shared memory `/dev/shm` mount params:

```bash
tmpfs  /dev/shm  tmpfs  rw,nodev,nosuid,noexec,size=1024M,mode=1777 0 0
```

  > You can also create a group named 'shm' and put application users for SHM-using applications in there. Then the access can be completely be restricted as such:

```bash
tmpfs  /dev/shm  tmpfs  rw,nodev,nosuid,noexec,size=1024M,mode=1770,uid=root,gid=shm 0 0
```

##### Secure /proc filesystem

The proc pseudo-filesystem `/proc` should be mounted with `hidepid`. When setting `hidepid` to **2**, directories entries in `/proc` will hidden.

```bash
proc  /proc  proc  defaults,hidepid=2  0 0
```

  > Some of the services/programs operate incorrectly when the `hidepid` parameter is set, e.g. Nagios checks.

##### Swap partition

This chapter does not talk about mounting options, but you should also mention the `swap` partition.

Encryption of swap space is used to protect sensitive information. It improves the availability of the system, which is also an important part of information security.

```bash
# Turn off the swap area
swapoff -a

# Wipe the swap area
shred -vfz -n 10 /dev/sda2

# Update /etc/fstab
UUID=7e1e715e-7ac4-45ad-b029-18fed80f225f none swap defaults 0 0

# Add the swap area to /etc/crypttab
swap /dev/sda2 /dev/urandom swap

# Activate the mapping
cryptdisks_start swap
/etc/init.d/cryptdisks restart

# Add the encrypted swap area to /etc/fstab
/dev/mapper/swap none swap defaults 0 0

# Turn on the swap area
swapon -a
```

#### Useful resources

- [Linux Security: Mount /tmp With nodev, nosuid, and noexec Options](https://www.cyberciti.biz/faq/linux-add-nodev-nosuid-noexec-options-to-temporary-storage-partitions/)
- [Security Handbook/Mounting partitions](https://wiki.gentoo.org/wiki/Security_Handbook/Mounting_partitions)
- [Increasing Linux server security with nodev, nosuid and no exec options](https://kb.iweb.com/hc/en-us/articles/230267488--Increasing-Linux-server-security-with-nodev-nosuid-and-no-exec-options)
- [Why it is important to Securing /dev/shm and /tmp](https://askubuntu.com/questions/389408/why-it-is-important-to-securing-dev-shm-and-tmp)
- [Securing /dev/shm partition](https://www.gnutoolbox.com/securing-devshm-partition/)
- [Linux system hardening: adding hidepid to /proc mount point](https://linux-audit.com/linux-system-hardening-adding-hidepid-to-proc/)
- [dm-crypt/Swap encryption](https://wiki.archlinux.org/index.php/Dm-crypt/Swap_encryption)
- [Encrypted swap partition on Debian/Ubuntu](https://feeding.cloud.geek.nz/posts/encrypted-swap-partition-on/)
