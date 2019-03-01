You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[System stack](#system-stack)**

## System stack

The SSH protocol is recommended for remote login and remote file transfer. SSH provides confidentiality and integrity for data exchanged between two systems, as well as server authentication, through the use of public key cryptography.

### Disable empty passwords

#### Rationale

Configuring this setting for the SSH daemon provides additional assurance that remote login via SSH will require a password, even in the event of misconfiguration elsewhere.

#### Solution

###### Explicitly disallow SSH login from accounts with empty passwords

```bash
PermitEmptyPasswords no
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_disable_empty_passwords">C2S/CIS: CCE-27471-2 (High)</a></sup>