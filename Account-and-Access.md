You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Accounts and Access](#accounts-and-access)**
  * [PAM Module](#pam-module)
  * [Physical console access](#)
  * [Session configuration files](#)
  * [Banners](#)
  * [Passwords policy](#)

## Accounts and Access

### PAM Module

#### Rationale

Linux-PAM is a library that enables the local system administrator to choose how individual applications authenticate users. It offers multiple low-level authentication schemes into a high-level application programming interface (API).

#### Solution

###### Password hashing algorithm

```bash
# C2S/CIS: CCE-27104-9 (Medium)

password  sufficient  pam_unix.so sha512 other arguments...
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_set_password_hashing_algorithm_systemauth">CCE-27104-9 (Medium)</a></code>

#### Comments

##### Mount options



#### Useful resources

- [Using Pluggable Authentication Modules (PAM)](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/managing_smart_cards/pluggable_authentication_modules) <sup>[Official]</sup>
