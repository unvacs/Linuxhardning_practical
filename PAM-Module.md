You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[PAM Module](#pam-module)**
  * [Password hashing algorithm](#password-hashing-algorithm)
  * [Failed password attempts](#failed-password-attempts)
  * [Limit password reuse](#limit-password-reuse)
  * [Password quality requirements](#password-quality-requirements)

## PAM Module

Linux-PAM is a library that enables the local system administrator to choose how individual applications authenticate users. It offers multiple low-level authentication schemes into a high-level application programming interface (API).

Before start this chapter please see official Red Hat documentation - [Using Pluggable Authentication Modules (PAM)](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/managing_smart_cards/pluggable_authentication_modules).

### Password hashing algorithm

#### Rationale

Currently more used is SHA-512 based hash (`sha512crypt`), which is similar in structure to `md5crypt` and `sha256crypt` and but support variable amounts of iteration. It's marked with and `$6$` respectively. `sha512crypt` (`$6$`) is what at least RedHat/CentOS and Debian (generally most modern distros) currently use by default.

#### Solution

###### Set properly password hashes in /etc/shadow

```bash
# C2S/CIS: CCE-27104-9 (Medium)

password sufficient pam_unix.so sha512 shadow nullok try_first_pass use_authtok
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
# C2S/CIS: CCE-26884-7 (Medium), CCE-27350-8 (Medium)

# Add the following line immediately before the pam_unix.so
auth required pam_faillock.so preauth silent deny=5 unlock_time=900 fail_interval=900

# Add the following line immediately after the pam_unix.so
auth [default=die] pam_faillock.so authfail deny=5 unlock_time=900 fail_interval=900

# Add the following line immediately before the pam_unix.so
account required pam_faillock.so
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_passwords_pam_faillock_unlock_time">CCE-26884-7 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_passwords_pam_faillock_deny">CCE-27350-8 (Medium)</a></code>

#### Comments

You can use a more restrictive configuration (I personally prefer this way):

```bash
auth required pam_faillock.so preauth silent deny=3 unlock_time=1800 fail_interval=900

auth [default=die] pam_faillock.so authfail deny=3 unlock_time=1800 fail_interval=900
```

Other guides recommend setting the `FAILLOG_ENAB` and `FAIL_DELAY` params in `/etc/login.defs` configuration file. It's incorrect solution beacuse `login.defs` is no longer used by `login`, `su` and `passwd` (see man for `login.defs(5)`) unless you use `pam_pwcheck`.

#### Useful resources

- [How to Lock User Accounts After Failed Login Attempts](https://www.tecmint.com/lock-user-accounts-after-failed-login-attempts-in-linux/)

### Limit password reuse

#### Rationale

Password history policy will set how often an old password can be reused so do not allow users to reuse recent passwords. Preventing re-use of previous passwords helps ensure that a compromised password is not re-used by a user.

  > The DoD STIG requirement is 5 passwords.

#### Solution

###### Set password reuse limit

Edit `pam_unix.so` or `pam_pwhistory.so` lines in `/etc/pam.d/system-auth`:

```bash
# C2S/CIS: CCE-26923-3 (Medium)

# For the pam_unix.so:
password sufficient pam_unix.so sha512 shadow nullok try_first_pass use_authtok remember=5

# For the pam_pwhistory.so:
password required pam_pwhistory.so debug use_authtok remember=5
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_pam_unix_remember">CCE-26923-3 (Medium)</a></code>

#### Comments

OWASP-OTG-AUTHN-007 provide great password policy solutions (sorry for copy-paste but it's really amazing):

- What characters are permitted and forbidden for use within a password? Is the user required to use characters from different character sets such as lower and uppercase letters, digits and special symbols?

- How often can a user change their password? How quickly can a user change their password after a previous change? Users may bypass password history requirements by changing their password 5 times in a row so that after the last password change they have configured their initial password again.

- When must a user change their password? After 90 days? After account lockout due to excessive log on attempts?

- How often can a user reuse a password? Does the application maintain a history of the user's previous used 8 passwords?

- How different must the next password be from the last password?

- Is the user prevented from using his username or other account information (such as first or last name) in the password?

#### Useful resources

- [RHEL 7 and pam_pwhistory - old password can still be re-used](https://access.redhat.com/discussions/2484201) <sup>[Official]</sup>
- [OWASP (OTG-AUTHN-007)](https://www.owasp.org/index.php/Testing_for_Weak_password_policy_(OTG-AUTHN-007))

### Password quality requirements

#### Rationale

The `pam_pwquality` PAM module can be configured to meet requirements for a variety of policies.

  > C2S/CIS allows modified these arguments to ensure compliance with your organization's security policy.

Use of a complex or strength password helps to increase the time and resources required to compromise the password.

- `minlen` parameter controls requirements for minimum characters required in a password. The shorter the password, the lower the number of possible combinations that need to be tested before the password is compromised.
- `dcredit` parameter controls requirements for usage of digits in a password.
- `lcredit` parameter controls requirements for usage of lowercase letters in a password.
- `ucredit` parameter controls requirements for usage of uppercase letters in a password.

#### Solution

###### Check pam_pwquality and set password retry prompts

Setting the password retry prompts that are permitted on a per-session basis to a low value requires some software, such as SSH, to re-connect.

  > The DoD requirement is a maximum of 3 prompts per session.

```bash
# C2S/CIS: CCE-27160-1 (Unknown)

# Edit /etc/pam.d/system-auth:
password requisite pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
```

###### Set password minimum length

```bash
# C2S/CIS: CCE-27293-0 (Medium)

# Edit /etc/security/pwquality.conf:
minlen = 14
```

###### Set password strength

```bash
# C2S/CIS: CCE-27214-6 (Medium), CCE-27345-8 (Medium), CCE-27200-5 (Medium)

# Edit /etc/security/pwquality.conf:
dcredit = -1
lcredit = -1
ucredit = -1
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_pam_minlen">CCE-27293-0 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_pam_dcredit">CCE-27214-6 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_pam_lcredit">CCE-27345-8 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_pam_ucredit">CCE-27200-5 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_pam_retry">CCE-27160-1 (Unknown)</a></code>

#### Comments

Official C2S/CIS standard also explain the following parameters. However, it doesn't specify identifiers for them.

- `difok` sets the minimum number of characters that must be different from the previous password. If you increase `minlen`, you may also want to increase this value as well.
- `ocredit` sets the maximum credit for having other characters in the new password.
- `maxrepeat` reject passwords which contain more than N same consecutive characters.

```bash
difok = 4
ocredit = -1
maxrepeat = 3
```

If you want to check password strengths, you should use `cracklib-check`:

```bash
cat > ~/passwd-test << __EOF__
for i in aaa password \$RANDOM \$(pwgen 12) ighu6zaivoomahPhah ; do

  echo -en "Check password: \$i\\n"
  echo "\$i" | cracklib-check

done
__EOF__

$ bash ~/passwd-test 
Check password: aaa
aaa: it is WAY too short
Check password: password
password: it is based on a dictionary word
Check password: 29997
29997: it is too short
Check password: Ociechai2moh
Ociechai2moh: OK
Check password: ighu6zaivoomahPhah
ighu6zaivoomahPhah: OK
```

#### Useful resources

- [Set a password policy in Red Hat Enterprise Linux 7](https://access.redhat.com/solutions/2808101) <sup>[Official]</sup>
- [How to configure password complexity for all users including root using pam_passwdqc.so](https://access.redhat.com/solutions/23481) <sup>[Official]</sup>
- [Hardening Your System With Tools and Services](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/security_guide/chap-hardening_your_system_with_tools_and_services) <sup>[Official]</sup>
- [Configuring Services: PAM](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/configuration_options-pam_configuration_options) <sup>[Official]</sup>