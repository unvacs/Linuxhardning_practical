You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[SELinux](#selinux)**
  * [Ensure SELinux state is enforcing](#ensure-selinux-state-is-enforcing)
  * [Ensure SELinux not disabled in /etc/default/grub](#ensure-selinux-not-disabled-in-etc-default-grub)
  * [Ensure no daemons are unconfined by SELinux](#ensure-no-daemons-are-unconfined-by-selinux)
  * [Uninstall mcstrans package](#uninstall-mcstrans-package)

## SELinux

 SELinux is a feature of the Linux kernel which can be used to guard against misconfigured or compromised programs. SELinux enforces the idea that programs should be limited in what files they can access and what actions they can take.

### Ensure SELinux state is enforcing

#### Rationale

Setting the SELinux state to enforcing ensures SELinux is able to confine potentially compromised processes to the security policy, which is designed to prevent them from causing damage to the system or further elevating their privileges.

#### Solution

```bash
# Edit /etc/selinux/config:
SELINUX=enforcing
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_selinux_state">C2S/CIS: CCE-27334-2 (High)</a></sup>

#### Useful resources

- []()

### Ensure SELinux not disabled in /etc/default/grub

#### Rationale

Disabling a major host protection feature, such as SELinux, at boot time prevents it from confining system services at boot time. Further, it increases the chances that it will remain off during system operation.

#### Solution

```bash
# Remove from /etc/default/grub:
selinux=0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_grub2_enable_selinux">C2S/CIS: CCE-26961-3 (Medium)</a></sup>

#### Useful resources

- []()

### Ensure no daemons are unconfined by SELinux

#### Rationale

Daemons which run with the initrc_t context may cause AVC denials, or allow privileges that the daemon does not require.

#### Solution

```bash
ps -eZ | egrep "initrc" | egrep -vw "tr|ps|egrep|bash|awk" | tr ':' ' ' | awk '{ print $NF }'
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_selinux_confinement_of_daemons">C2S/CIS: CCE-27288-0 (Medium)</a></sup>

#### Useful resources

- []()

### Uninstall mcstrans package

#### Rationale

Since this service is not used very often, disable it to reduce the amount of potentially vulnerable code running on the system. NOTE: This rule was added in support of the CIS RHEL6 v1.2.0 benchmark. Please note that Red Hat does not feel this rule is security relevant.

#### Solution

```bash
yum erase mcstrans
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_package_mcstrans_removed">C2S/CIS: CCE-80445-0 (Unknown)</a></sup>

#### Useful resources

- []()
