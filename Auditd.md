You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Auditd](#auditd)**
  * [Max log file size](#max-log-file-size)
  * [Notification on low disk space](#notification-on-low-disk-space)
  * [Action on low disk space](#action-on-low-disk-space)
  * [Action upon reaching maximum log size](#action-upon-reaching-maximum-log-size)
  * [Record information on kernel module loading and unloading](#collects-information-on-kernel-module-loading-and-unloading)
  * [Record attempts to alter logon and logout events](#record-attempts-to-alter-logon-and-logout-events)
  * [Record attempts to alter time through stime](#record-attempts-to-alter-time-through-stime)
  * [Record attempts to alter time through settimeofday](#record-attempts-to-alter-time-through-settimeofday)
  * [Record attempts to alter the localtime file](#record-attempts-to-alter-the-localtime-file)
  * [Record attempts to alter time through clock_settime](#record-attempts-to-alter-time-through-clock_settime)
  * [Record attempts to alter time through adjtimex](#record-attempts-to-alter-time-through-adjtimex)
  * [Record events that modify the system's discretionary access controls](#record-events-that-modify-the-systems-discretionary-access-controls)
  * [Ensure auditd collects file deletion events by user](#ensure-auditd-collects-file-deletion-events-by-user)
  * [Record information on the use of privileged commands](#record-information-on-the-use-of-privileged-commands)
  * [Record unauthorized access attempts to files](#record-unauthorized-access-attempts-to-files)

## Auditd

The audit service provides substantial capabilities for recording system activities.

By default, the service audits about SELinux AVC denials and certain types of security-relevant events such as system logins, account modifications, and authentication events performed by programs such as sudo.

### Max log file size

#### Rationale

The total storage for audit log files must be large enough to retain log information over the period required. This is a function of the maximum log file size and the number of logs retained.

#### Solution

###### Set the value

```bash
# Edit /etc/audit/auditd.conf:
max_log_file = STOREMB
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_auditd_data_retention_max_log_file">C2S/CIS: CCE-27319-3 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Notification on low disk space

#### Rationale

Email sent to the root account is typically aliased to the administrators of the system, who can take appropriate action.

#### Solution

###### Set the value

```bash
# Edit /etc/audit/auditd.conf:
action_mail_acct = root
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_auditd_data_retention_action_mail_acct">C2S/CIS: CCE-27394-6 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Action on low disk space

#### Rationale

Administrators should be made aware of an inability to record audit records. If a separate partition or logical volume of adequate size is used, running low on space for audit records should never occur.

#### Solution

###### Set the value

```bash
# Edit /etc/audit/auditd.conf:
admin_space_left_action = ACTION
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_auditd_data_retention_admin_space_left_action">C2S/CIS: CCE-27370-6 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Action upon reaching maximum log size

#### Rationale

Automatically rotating logs (by setting this to rotate) minimizes the chances of the system unexpectedly running out of disk space by being overwhelmed with log data.

However, for systems that must never discard log data, or which use external processes to transfer it and reclaim space, keep_logs can be employed.

#### Solution

###### Set the value

```bash
# Edit /etc/audit/auditd.conf:
max_log_file_action = ACTION
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_auditd_data_retention_max_log_file_action">C2S/CIS: CCE-27231-0 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Collects information on kernel module loading and unloading

#### Rationale

The addition/removal of kernel modules can be used to alter the behavior of the kernel and potentially introduce malicious code into kernel space. It is important to have an audit trail of modules that have been introduced into the kernel.

#### Solution

###### Set the value

```bash
# Add to /etc/audit/rules.d/extended.rules
-w /usr/sbin/insmod -p x -k modules
-w /usr/sbin/rmmod -p x -k modules
-w /usr/sbin/modprobe -p x -k modules

-a always,exit -F arch=ARCH -S init_module,finit_module,create_module,delete_module -F key=modules
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_kernel_module_loading">C2S/CIS: CCE-27129-6 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Record attempts to alter logon and logout events

#### Rationale

Manual editing of these files may indicate nefarious activity, such as an attacker attempting to remove evidence of an intrusion.

#### Solution

###### Set the value

```bash
# Add to /etc/audit/rules.d/extended.rules
-w /var/log/tallylog -p wa -k logins
-w /var/run/faillock -p wa -k logins
-w /var/log/lastlog -p wa -k logins
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_login_events">C2S/CIS: CCE-27204-7 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Record attempts to alter time through stime

#### Rationale

Arbitrary changes to the system time can be used to obfuscate nefarious activities in log files, as well as to confuse network services that are highly dependent upon an accurate system time (such as sshd). All changes to the system time should be audited.

#### Solution

###### Set the value

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S stime -F key=audit_time_rules
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_time_stime">C2S/CIS: CCE-27299-7 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Record attempts to alter time through settimeofday

#### Rationale

Arbitrary changes to the system time can be used to obfuscate nefarious activities in log files, as well as to confuse network services that are highly dependent upon an accurate system time (such as sshd). All changes to the system time should be audited.

#### Solution

###### Set the value

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S settimeofday -F key=audit_time_rules
-a always,exit -F arch=b64 -S settimeofday -F key=audit_time_rules
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_time_settimeofday">C2S/CIS: CCE-27216-1 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Record attempts to alter the localtime file

#### Rationale

Arbitrary changes to the system time can be used to obfuscate nefarious activities in log files, as well as to confuse network services that are highly dependent upon an accurate system time (such as sshd). All changes to the system time should be audited.

#### Solution

###### Set the value

```bash
# Add to /etc/audit/rules.d/extended.rules
-w /etc/localtime -p wa -k audit_time_rules
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_time_watch_localtime">C2S/CIS: CCE-27310-2 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Record attempts to alter time through clock_settime

#### Rationale

Arbitrary changes to the system time can be used to obfuscate nefarious activities in log files, as well as to confuse network services that are highly dependent upon an accurate system time (such as sshd). All changes to the system time should be audited.

#### Solution

###### Set the value

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S clock_settime -F a0=0x0 -F key=time-change
-a always,exit -F arch=b64 -S clock_settime -F a0=0x0 -F key=time-change
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_time_clock_settime">C2S/CIS: CCE-27219-5 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Record attempts to alter time through adjtimex

#### Rationale

Arbitrary changes to the system time can be used to obfuscate nefarious activities in log files, as well as to confuse network services that are highly dependent upon an accurate system time (such as sshd). All changes to the system time should be audited.

#### Solution

###### Set the value

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S adjtimex -F key=audit_time_rules
-a always,exit -F arch=b64 -S adjtimex -F key=audit_time_rules
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_time_adjtimex">C2S/CIS: CCE-27290-6 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Record events that modify the system's discretionary access controls

#### Rationale

The changing of file permissions could indicate that a user is attempting to gain access to information that would otherwise be disallowed. Auditing DAC modifications can facilitate the identification of patterns of abuse among both authorized and unauthorized users.

#### Solution

###### fchown

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S fchown -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S fchown -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_fchown">C2S/CIS: CCE-27356-5 (Medium)</a></sup>

###### setxattr

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S setxattr -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S setxattr -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_setxattr">C2S/CIS: CCE-27213-8 (Medium)</a></sup>

###### chown

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S chown -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S chown -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_chown">C2S/CIS: CCE-27364-9 (Medium)</a></sup>

###### removexattr

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S removexattr -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S removexattr -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_removexattr">C2S/CIS: CCE-27367-2 (Medium)</a></sup>

###### fchownat

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S fchownat -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S fchownat -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_fchownat">C2S/CIS: CCE-27387-0 (Medium)</a></sup>

###### chmod

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S chmod -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S chmod -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_chmod">C2S/CIS: CCE-27339-1 (Medium)</a></sup>

###### fsetxattr

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S fsetxattr -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S fsetxattr -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_fsetxattr">C2S/CIS: CCE-27389-6 (Medium)</a></sup>

###### fchmod

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S fchmod -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S fchmod -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_fchmod">C2S/CIS: CCE-27393-8 (Medium)</a></sup>

###### lsetxattr

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S lsetxattr -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S lsetxattr -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_lsetxattr">C2S/CIS: CCE-27280-7 (Medium)</a></sup>

###### fremovexattr

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S fremovexattr -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S fremovexattr -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_fremovexattr">C2S/CIS: CCE-27353-2 (Medium)</a></sup>

###### lchown

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S lchown -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S lchown -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_lchown">C2S/CIS: CCE-27083-5 (Medium)</a></sup>

###### fchmodat

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S fchmodat -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b64 -S fchmodat -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_fchmodat">C2S/CIS: CCE-27388-8 (Medium)</a></sup>

###### lremovexattr

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S lremovexattr -F auid>=1000 -F auid!=unset -F key=perm_mod
-a always,exit -F arch=b32 -S lremovexattr -F auid>=1000 -F auid!=unset -F key=perm_mod
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_dac_modification_lremovexattr">C2S/CIS: CCE-27410-0 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Ensure auditd collects file deletion events by user

#### Rationale

Auditing file deletions will create an audit trail for files that are removed from the system. The audit trail could aid in system troubleshooting, as well as, detecting malicious processes that attempt to delete log files to conceal their presence.

#### Solution

###### unlinkat

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=ARCH -S unlinkat -F auid>=1000 -F auid!=unset -F key=delete
-a always,exit -F arch=ARCH -S unlinkat -F auid>=1000 -F auid!=unset -F key=delete
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_file_deletion_events_unlinkat">C2S/CIS: CCE-80662-0 (Medium)</a></sup>

###### rename

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=ARCH -S rename -F auid>=1000 -F auid!=unset -F key=delete
-a always,exit -F arch=ARCH -S rename -F auid>=1000 -F auid!=unset -F key=delete
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_file_deletion_events_rename">C2S/CIS: CCE-27206-2 (Medium)</a></sup>

###### renameat

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=ARCH -S renameat -F auid>=1000 -F auid!=unset -F key=delete
-a always,exit -F arch=ARCH -S renameat -F auid>=1000 -F auid!=unset -F key=delete
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_file_deletion_events_renameat">C2S/CIS: CCE-80413-8 (Medium)</a></sup>

###### unlink

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=ARCH -S unlink -F auid>=1000 -F auid!=unset -F key=delete
-a always,exit -F arch=ARCH -S unlink -F auid>=1000 -F auid!=unset -F key=delete
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_file_deletion_events_unlink">C2S/CIS: CCE-27206-2 (Medium)</a></sup>

#### Comments

#### Useful resources

- []()

### Record information on the use of privileged commands

#### Rationale

Privileged programs are subject to escalation-of-privilege attacks, which attempt to subvert their normal role of providing some necessary but limited capability. As such, motivation exists to monitor these programs for unusual activity.

#### Solution

###### unlinkat

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F path=SETUID_PROG_PATH -F perm=x -F auid>=1000 -F auid!=unset -F key=privileged
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_privileged_commands">C2S/CIS: CCE-27437-3 (Medium)</a></sup>

#### Comments

To find the relevant `setuid` and `setgid` programs:

```bash
find / -xdev -type f -perm -4000 -o -type f -perm -2000 2>/dev/null
```

#### Useful resources

- []()

### Record unauthorized access attempts to files

#### Rationale

Unsuccessful attempts to access files could be an indicator of malicious activity on a system. Auditing these events could serve as evidence of potential system compromise.

#### Solution

###### truncate

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S truncate -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b32 -S truncate -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S truncate -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S truncate -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_unsuccessful_file_modification_truncate">C2S/CIS: CCE-80389-0 (Medium)</a></sup>

###### creat

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S creat -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b32 -S creat -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S creat -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S creat -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_unsuccessful_file_modification_creat">C2S/CIS: CCE-80385-8 (Medium)</a></sup>

###### open

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S open -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b32 -S open -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S open -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S open -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_unsuccessful_file_modification_open">C2S/CIS: CCE-80386-6 (Medium)</a></sup>

###### open_by_handle_at

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S open_by_handle_at -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b32 -S open_by_handle_at -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S open_by_handle_at -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S open_by_handle_at -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_unsuccessful_file_modification_open_by_handle_at">C2S/CIS: CCE-80388-2 (Medium)</a></sup>

###### ftruncate

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S ftruncate -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b32 -S ftruncate -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S ftruncate -F exiu=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S ftruncate -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_unsuccessful_file_modification_ftruncate">C2S/CIS: CCE-80390-8 (Medium)</a></sup>

###### openat

```bash
# Add to /etc/audit/rules.d/extended.rules
-a always,exit -F arch=b32 -S openat -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b32 -S openat -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S openat -F exit=-EACCES -F auid>=1000 -F auid!=unset -F key=access
-a always,exit -F arch=b64 -S openat -F exit=-EPERM -F auid>=1000 -F auid!=unset -F key=access
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_audit_rules_unsuccessful_file_modification_openat">C2S/CIS: CCE-80387-4 (Medium)</a></sup>

#### Comments


#### Useful resources

- []()
