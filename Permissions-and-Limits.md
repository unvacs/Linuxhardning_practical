You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Permissions and Limits](#permissions-and-limits)**
  * [Verify permissions](#verify-permissions)
  * [Restrict filesystems](#restrict-filesystems)
  * [Restrict programs](#restrict-programs)

## Permissions and Limits

Traditional Unix security relies heavily on file and directory permissions to prevent unauthorized users from reading or modifying files to which they should not have access.

### Verify permissions

#### Rationale

Permissions for many files on a system must be set restrictively to ensure sensitive information is properly protected. This section discusses important permission restrictions which can be verified to ensure that no harmful discrepancies have arisen.

The default restrictive permissions for files which act as important security databases such as `passwd`, `shadow`, `group`, and `gshadow` files must be maintained.

#### Solution

###### Verify permissions and owners

```bash
chmod 0644 /etc/passwd
chown root /etc/passwd
chgrp root /etc/passwd
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_etc_group">C2S/CIS: CCE-26949-8 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_etc_passwd">C2S/CIS: CCE-26887-0 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_etc_passwd">C2S/CIS: CCE-27138-7 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_groupowner_etc_passwd">C2S/CIS: CCE-26639-5 (Medium)</a></sup>

```bash
chown root /etc/group
chgrp root /etc/group
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_etc_group">C2S/CIS: CCE-26933-2</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_groupowner_etc_group">C2S/CIS: CCE-27037-1 (Medium)</a></sup>

```bash
chmod 0640 /etc/shadow
chown root /etc/shadow
chgrp root /etc/shadow
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_etc_shadow">C2S/CIS: CCE-27100-7 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_etc_shadow">C2S/CIS: CCE-26795-5 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_groupowner_etc_shadow">C2S/CIS: CCE-27125-4 (Medium)</a></sup>

```bash
chmod 0000 /etc/gshadow
chown root /etc/gshadow
chgrp root /etc/gshadow
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_etc_gshadow">C2S/CIS: CCE-27162-7 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_etc_gshadow">C2S/CIS: CCE-27161-9 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_groupowner_etc_gshadow">C2S/CIS: CCE-26840-9 (Medium)</a></a></sup>

#### Comments

For the following recommendations C2S/CIS does not provide a solution, but there are methods to verify it.

###### Ensure all files are owned by a user and group

Unowned files may be caused by an intruder, by incorrect software installation or draft software removal, or by failure to remove all files belonging to a deleted account.

```bash
# To find out all files that are not owned by any user:
find / -nouser

# To find out all files that are not owned by any group:
find / -nogroup
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_no_files_unowned_by_user">C2S/CIS: CCE-80134-0 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_ungroupowned">C2S/CIS: CCE-80135-7 (Medium)</a></sup>

###### Ensure no world-writable files exist

Data in world-writable files can be modified by any user on the system. In almost all circumstances, files can be configured using a combination of user and group permissions to support whatever legitimate access is needed without the risk caused by world-writable files.

  > Check with documentation for specific applications before making changes.

```bash
# To find all files that are world writable, irrespective of what other permissions they have, you can do:
find / -perm -o+w
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_unauthorized_world_writable">C2S/CIS: CCE-80131-6 (Medium)</a></sup>

###### Verify that all world-writable directories have sticky bits set

Failing to set the sticky bit on public directories allows unauthorized users to delete files in the directory structure.

```bash
# To verify no world writable directories exist without the sticky bit set:
df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -type d \( -perm -0002 -a ! -perm -1000 \) 2>/dev/null
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_dir_perms_world_writable_sticky_bits">C2S/CIS: CCE-80130-8 (Unknown)</sup>

#### Useful resources

### Restrict filesystems

#### Rationale

Linux includes a number of facilities for the automated addition and removal of filesystems on a running system. These facilities may be necessary in many environments, but this capability also carries some risk.

#### Solution

###### Restrict dynamic mounting and unmounting

```bash
# Add to /etc/modprobe.d/fs-blacklist.conf:
install freevxfs /bin/true
install udf /bin/true
install squashfs /bin/true
install hfsplus /bin/true
install jffs2 /bin/true
install hfs /bin/true
install cramfs /bin/true
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_kernel_module_freevxfs_disabled">C2S/CIS: CCE-80138-1 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_kernel_module_udf_disabled">C2S/CIS: CCE-80143-1 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_kernel_module_squashfs_disabled">C2S/CIS: CCE-80142-3 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_kernel_module_hfsplus_disabled">C2S/CIS: CCE-80141-5 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_kernel_module_jffs2_disabled">C2S/CIS: CCE-80139-9 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_kernel_module_hfs_disabled">C2S/CIS: CCE-80140-7 (Low)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_kernel_module_cramfs_disabled">C2S/CIS: CCE-80137-3 (Low)</a></sup>

```bash
# And disable automounter:
systemctl disable autofs.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_autofs_disabled">CCE-27498-5 (Medium)</a></sup>

#### Comments

It is sensible to leave only the modules you use. If you don't use nfs, cifs etc. you may also disable the following file systems:

```bash
install fat /bin/true
install vfat /bin/true
install cifs /bin/true
install nfs /bin/true
install nfsv3 /bin/true
install nfsv4 /bin/true
install gfs2 /bin/true
```

#### Useful resources

### Restrict programs

#### Rationale

The recommendations in this section are designed to ensure that the system's features to protect against potentially dangerous program execution are activated.

#### Solution

###### Disable core dumps

The core dump files may also contain sensitive information, or unnecessarily occupy large amounts of disk space.

You can restrict access to core dumps to certain users or groups, as described in the limits.conf(5) manual page.

```bash
# Add to /etc/sysctl.d/hardening.conf:
fs.suid_dumpable = 0

# Edit /etc/security/limits.conf:
*     hard   core    0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_fs_suid_dumpable">C2S/CIS: CCE-26900-1 (Unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_disable_users_coredumps">C2S/CIS: CCE-80169-6 (Unknown)</a></sup>

###### Enable ExecShield

ExecShield describes kernel features that provide protection against exploitation of memory corruption errors such as buffer overflows.

  > Exec Shield is enabled in CentOS-6 and 7 by default. To view the list of running processes that have exec-shield enabled, you can run [lsexec](https://people.redhat.com/sgrubb/files/lsexec) tool.

It's designed to limit against:

- stack
- buffer
- function pointer overflows

```bash
# Add to /etc/sysctl.d/hardening.conf:
# kernel.exec-shield = 1
kernel.randomize_va_space = 2
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_kernel_exec_shield">C2S/CIS: CCE-27211-2 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_kernel_randomize_va_space">C2S/CIS: CCE-27127-0 (Medium)</a></sup>

#### Comments

When you combine exec-shield, SELinux and proper patching and security best practices you can really limit the attack vectors that can be used to break into your systems.

#### Useful resources

- [Understand and configure core dumps on Linux](https://linux-audit.com/understand-and-configure-core-dumps-work-on-linux/)
- [Security Technologies: ExecShield](https://access.redhat.com/blogs/766093/posts/3534821)
