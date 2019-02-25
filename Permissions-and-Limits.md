You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Permissions and Limits](#permissions-and-limits)**
  * [Verify permissions](#verify-permissions)
  * [Restrict partitions](#restrict-partitions)
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
# C2S/CIS: CCE-26949-8 (Medium) and CCE-26887-0 (Medium), CCE-27138-7 (Medium), CCE-26639-5 (Medium)
chmod 0644 /etc/passwd
chown root /etc/passwd
chgrp root /etc/passwd

# C2S/CIS: CCE-26933-2 (Medium), CCE-27037-1 (Medium)
chown root /etc/group
chgrp root /etc/group

# C2S/CIS: CCE-27100-7 (Medium), CCE-26795-5 (Medium), CCE-27125-4 (Medium)
chmod 0640 /etc/shadow
chown root /etc/shadow
chgrp root /etc/shadow

# C2S/CIS: CCE-27162-7 (Medium), CCE-27161-9 (Medium), CCE-26840-9 (Medium)
chmod 0000 /etc/gshadow
chown root /etc/gshadow
chgrp root /etc/gshadow
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_etc_group">CCE-26949-8 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_etc_passwd">CCE-26887-0 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_etc_passwd">CCE-27138-7 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_groupowner_etc_passwd">CCE-26639-5 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_etc_group">CCE-26933-2</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_groupowner_etc_group">CCE-27037-1 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_etc_shadow">CCE-27100-7 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_etc_shadow">CCE-26795-5 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_groupowner_etc_shadow">CCE-27125-4 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_permissions_etc_gshadow">CCE-27162-7 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_owner_etc_gshadow">CCE-27161-9 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_file_groupowner_etc_gshadow">CCE-26840-9 (Medium)</a></code>

#### Comments

#### Useful resources

- []()

### Restrict partitions

#### Rationale

#### Solution

###### /dev/shm

```bash
# C2S/CIS: CCE-80153-0 (unknown)


```

#### Policies

<code>C2S/CIS: <a href=""></a></code>

#### Comments

#### Useful resources

- []()

### Restrict filesystems

#### Rationale

#### Solution

###### /dev/shm

```bash
# C2S/CIS: CCE-80153-0 (unknown)


```

#### Policies

<code>C2S/CIS: <a href=""></a></code>

#### Comments

#### Useful resources

- []()

### Restrict programs

#### Rationale

#### Solution

###### /dev/shm

```bash
# C2S/CIS: CCE-80153-0 (unknown)


```

#### Policies

<code>C2S/CIS: <a href=""></a></code>

#### Comments

#### Useful resources

- []()