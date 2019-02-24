You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[PAM Module](#pam-module)**
  * [Password hashing algorithm](#password-hashing-algorithm)

## PAM Module

Linux-PAM is a library that enables the local system administrator to choose how individual applications authenticate users. It offers multiple low-level authentication schemes into a high-level application programming interface (API).

### Password hashing algorithm

#### Rationale

Currently more used are the SHA-256 and SHA-512 based hashes, `sha256crypt` and `sha512crypt`, which are similar in structure to `md5cryp`t but support variable amounts of iteration. They're marked with `$5$` and `$6$` respectively. `sha512crypt` (`$6$`) is what at least RedHat/CentOS and Debian (generally most modern distros)  currently use by default.

#### Solution

###### Set properly password hashes in /etc/shadow

```bash
# C2S/CIS: CCE-27104-9 (Medium)

password  sufficient  pam_unix.so sha512 other arguments...
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_set_password_hashing_algorithm_systemauth">CCE-27104-9 (Medium)</a></code>

#### Comments



#### Useful resources

- [Using Pluggable Authentication Modules (PAM)](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/managing_smart_cards/pluggable_authentication_modules) <sup>[Official]</sup>
- [About Secure Password Hashing](https://security.blogoverflow.com/2013/09/about-secure-password-hashing/)