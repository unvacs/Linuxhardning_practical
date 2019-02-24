You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[PAM Module](#pam-module)**
  * [Password hashing algorithm](#password-hashing-algorithm)
  * [Failed password attempts](#failed-password-attempts)

## PAM Module

Linux-PAM is a library that enables the local system administrator to choose how individual applications authenticate users. It offers multiple low-level authentication schemes into a high-level application programming interface (API).

Before start this chapter please see official RHEL documentation - [Using Pluggable Authentication Modules (PAM)](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/managing_smart_cards/pluggable_authentication_modules).

### Password hashing algorithm

#### Rationale

Currently more used are the SHA-256 and SHA-512 based hashes, `sha256crypt` and `sha512crypt`, which are similar in structure to `md5cryp`t but support variable amounts of iteration. They're marked with `$5$` and `$6$` respectively. `sha512crypt` (`$6$`) is what at least RedHat/CentOS and Debian (generally most modern distros)  currently use by default.

#### Solution

###### Set properly password hashes in /etc/shadow

```bash
# C2S/CIS: CCE-27104-9 (Medium)

password  sufficient  pam_unix.so sha512 shadow nullok try_first_pass use_authtok
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_set_password_hashing_algorithm_systemauth">CCE-27104-9 (Medium)</a></code>

#### Useful resources

- [About Secure Password Hashing](https://security.blogoverflow.com/2013/09/about-secure-password-hashing/)

### Failed password attempts

#### Rationale

This option provides the capability to lock out user accounts after a number of failed login attempts.

  > Locking out user accounts presents the risk of a denial-of-service attack.

#### Solution

###### Set lockout time

Edit `AUTH` and `ACCOUNT` (for the last parameter) section of both `/etc/pam.d/system-auth` and `/etc/pam.d/password-auth`:

```bash
# C2S/CIS: CCE-26884-7 (Medium)

# Add the following line immediately before the pam_unix.so
auth required pam_faillock.so preauth silent deny=5 unlock_time=900 fail_interval=900

# Add the following line immediately after the pam_unix.so
auth [default=die] pam_faillock.so authfail deny=5 unlock_time=900 fail_interval=900

# Add the following line immediately before the pam_unix.so
account required pam_faillock.so
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_passwords_pam_faillock_unlock_time">CCE-26884-7 (Medium)</a></code>

#### Comments

If you want, you can use a more restrictive configuration (I personally prefer this way):

```bash
auth required pam_faillock.so preauth silent deny=3 unlock_time=1800 fail_interval=900

auth [default=die] pam_faillock.so authfail deny=3 unlock_time=1800 fail_interval=900
```

Other guides recommend setting the `FAILLOG_ENAB` and `FAIL_DELAY` params in `/etc/login.defs` configuration file. It's incorrect solution beacuse `login.defs` is no longer used by `login`, `su` and `passwd` (see man for `login.defs(5)`) unless you use `pam_pwcheck`.

#### Useful resources

- [How to Lock User Accounts After Failed Login Attempts](https://www.tecmint.com/lock-user-accounts-after-failed-login-attempts-in-linux/)