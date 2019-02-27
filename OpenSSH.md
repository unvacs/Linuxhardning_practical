You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[OpenSSH](#openssh)**
  * [Disable empty passwords](#disable-empty-passwords)
  * [Idle timeout](#login-will-be-terminated)
  * [Idle timeout interval](#idle-timeout-interval)
  * [Warning banner](#warning-banner)
  * [Hash algorithms](#hash-algorithms)
  * [Environment options](#environment-options)
  * [Protocol version](#protocol-version)
  * [Support for .rhosts](#support-for-rhosts)
  * [Log levels](#log-levels)
  * [Validated ciphers](#validated-ciphers)
  * [Authentication](#authentication)
  * [Root login](#root-login)

## OpenSSH

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

### Idle timeout

#### Rationale

This ensures a user login will be terminated as soon as the `ClientAliveInterval` is reached.

#### Solution

###### Sets the number of client alive messages

```bash
ClientAliveCountMax 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_set_keepalive">C2S/CIS: CCE-27082-7 (Medium)</a></sup>

### Idle timeout interval

#### Rationale

Terminating an idle ssh session within a short time period reduces the window of opportunity for unauthorized personnel to take control of a management session enabled on the console or console port that has been let unattended.

#### Solution

###### Set short time period

```bash
ClientAliveInterval 300
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_set_idle_timeout">C2S/CIS: CCE-27433-2 (Medium)</a></sup>

### Warning banner

#### Rationale

The warning message reinforces policy awareness during the logon process and facilitates possible legal action against attackers.

#### Solution

###### Set short time period

```bash
Banner /etc/issue
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_enable_warning_banner">C2S/CIS: CCE-27314-4 (Medium)</a></sup>

### Hash algorithms

#### Rationale

DoD Information Systems are required to use FIPS-approved cryptographic hash functions. The only SSHv2 hash algorithms meeting this requirement is SHA2.

#### Solution

###### Use of FIPS-approved MACs

```bash
MACs hmac-sha2-512,hmac-sha2-256,hmac-sha1
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_use_approved_macs">C2S/CIS: CCE-27455-5 (Medium)</a></sup>

### Environment options

#### Rationale

SSH environment options potentially allow users to bypass access restriction in some configurations.

#### Solution

###### Override environment options

```bash
PermitUserEnvironment no
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_use_approved_macs">C2S/CIS: CCE-27363-1 (Medium)</a></sup>

### Protocol version

#### Rationale

SSH protocol version 1 is an insecure implementation of the SSH protocol and has many well-known vulnerability exploits. Exploits of the SSH daemon could provide immediate root access to the system.

#### Solution

###### Set correct protocol version

```bash
Protocol 2
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_use_approved_macs">C2S/CIS: CCE-27320-1 (High)</a></sup>

### Support for .rhosts

#### Rationale

SSH trust relationships mean a compromise on one host can allow an attacker to move trivially to other hosts.

#### Solution

###### Set correct protocol version

```bash
IgnoreRhosts yes
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_disable_rhosts">C2S/CIS: CCE-27377-1 (Medium)</a></sup>

### Log levels

#### Rationale

SSH provides several logging levels with varying amounts of verbosity. In many situations, such as _Incident Response_, it is important to determine when a particular user was active on a system. The `INFO` parameter specifices that record login and logout activity will be logged.

#### Solution

###### Set specify the log level

```bash
LogLevel INFO
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_set_loglevel_info">C2S/CIS: CCE-80645-5 (Medium)</a></sup>

### Validated ciphers

#### Rationale

Unapproved mechanisms that are used for authentication to the cryptographic module are not verified and therefore cannot be relied upon to provide confidentiality or integrity, and system data may be compromised.

#### Solution

###### Set algorithms which are FIPS-approved

```bash
Ciphers aes128-ctr,aes192-ctr,aes256-ctr,aes128-cbc,3des-cbc,aes192-cbc,aes256-cbc
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_use_approved_ciphers">C2S/CIS: CCE-27295-5 (High)</a></sup>

### Authentication

#### Rationale

SSH trust relationships mean a compromise on one host can allow an attacker to move trivially to other hosts.

Setting the MaxAuthTries parameter to a low number will minimize the risk of successful brute force attacks to the SSH server.

#### Solution

###### Disable host-based authentication

```bash
HostbasedAuthentication no
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_disable_host_auth">C2S/CIS: CCE-27413-4 (Medium)</a></sup>

###### Set authentication attempt limit

```bash
MaxAuthTries tries
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_set_max_auth_tries">C2S/CIS: No-CCE (Medium)</a></sup>

### Root login

#### Rationale

The root user should never be allowed to login to a system directly over a network.

#### Solution

###### Disable root login via SSH

```bash
PermitRootLogin no
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_disable_root_login">C2S/CIS: CCE-27445-6 (Medium)</a></sup>

#### Comments

The C2S/CIS standard also explain [X11Forwarding](https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sshd_set_loglevel_info) parameter. In my opinion we should definitely **disable** X session.

#### Useful resources

- [Configuring OpenSSH](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-ssh-configuration) <sup>[Official]</sup>
