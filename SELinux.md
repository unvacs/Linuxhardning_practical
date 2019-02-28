You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[SELinux](#selinux)**
  * [Ensure SELinux state is enforcing](#ensure-selinux-state-is-enforcing)

## SELinux

 SELinux is a feature of the Linux kernel which can be used to guard against misconfigured or compromised programs. SELinux enforces the idea that programs should be limited in what files they can access and what actions they can take.

### Ensure SELinux state is enforcing

#### Rationale

Setting the SELinux state to enforcing ensures SELinux is able to confine potentially compromised processes to the security policy, which is designed to prevent them from causing damage to the system or further elevating their privileges.

#### Solution

###### On all interfaces

```bash
# Edit /etc/selinux/config:
SELINUX=enforcing
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_selinux_state">C2S/CIS: CCE-27334-2 (High)</a></sup>

#### Useful resources

- []()
