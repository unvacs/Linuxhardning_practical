You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Accounts and Access](#accounts-and-access)**
  * [Physical console access](#physical-console-access)
  * [Session configuration files](#session-configuration-files)
  * [Banners](#banners)
  * [Passwords policy](#passwords-policy)

## Accounts and Access

In traditional Unix security, if an attacker gains shell access to a certain login account, they can perform any action or access any file to which that account has access.

Therefore, making it more difficult for unauthorized people to gain shell access to accounts, particularly to privileged accounts, is a necessary part of securing a system.

### Physical console access

#### Rationale

There are some steps which, if taken, make it more difficult for an attacker to quickly or undetectably modify a system from its console.

This is the easiest way to gain unauthorised access to a Linux system is to boot the server into single user mode. Attacker can select a kernel to boot from the grub menu item by pressing specific key to edit the boot option.

  > Remember to protect GRUB with password because it's the only way to protect single user mode in RedHat/CentOS distributions.

#### Solution

###### Authentication for single user mode

```bash
# C2S/CIS: CCE-27287-2 (Medium)

# Edit /usr/lib/systemd/system/rescue.service:
ExecStart=-/bin/sh -c "/usr/sbin/sulogin; /usr/bin/systemctl --fail --no-block default"
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_require_singleuser_auth">CCE-27287-2 (Medium)</a></code>

#### Comments

I also recommend change or set these options in `emergency.service`. It is default target when an issue kicks in during the boot process.

#### Useful resources

- [Console Access](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/deployment_guide/ch-console-access) <sup>[Official]</sup>

### Session configuration files

#### Rationale

When a user logs into a Unix account, the system configures the user's session by reading a number of files. Many of these files are located in the user's home directory, and may have weak permissions as a result of user error or misconfiguration.

#### Solution

###### Set sensible umask values

A misconfigured `umask` value could result in files with excessive permissions that can be read or written to by unauthorized users.

```bash
# C2S/CIS: CCE-80202-5 (unknown), CCE-80204-1 (unknown)

# Edit /etc/profile and /etc/bashrc
umask 027
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_umask_etc_bashrc">CCE-80202-5 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_umask_etc_profile">CCE-80204-1 (unknown)</a></code>

#### Comments

I have seen recommendations in some guides (and in the real configurations) to set the `umask 077` value. Many of Linux distributions are shipped by default with `umask 022`.

`umask 027` is better from security perspective. `umask 077` is even better to use for root (`077` means that noone but the owner is able to read or execute newly-created files).

I'm sure there's a perfectly rational explanation why it is used:

- it avoids some common system administrator mistakes
- it's harder for an attacker to run privilege escalation

The most obvious downside is when you start creating files/directories in a shared directory, expecting other users to access them.

#### Useful resources

- [Best practices for umask in Red Hat Enterprise Linux](https://access.redhat.com/solutions/107683) <sup>[Official]</sup>

### Banners

#### Rationale

Login banners provide a definitive warning to any possible intruders that may want to access your system that certain types of activity are illegal, but at the same time, it also advises the authorized and legitimate users of their obligations relating to acceptable use of the computerized or networked environment(s).

Pre-logon warning messages can deter unauthorized use, increase IT security awareness, and provide a legal basis for prosecuting unauthorized access.

#### Solution

The DoD required text is either:

```
You are accessing a U.S. Government (USG) Information System (IS) that is provided for USG-authorized use only. By using this IS (which includes any device attached to this IS), you consent to the following conditions:
-The USG routinely intercepts and monitors communications on this IS for purposes including, but not limited to, penetration testing, COMSEC monitoring, network operations and defense, personnel misconduct (PM), law enforcement (LE), and counterintelligence (CI) investigations.
-At any time, the USG may inspect and seize data stored on this IS.
-Communications using, or data stored on, this IS are not private, are subject to routine monitoring, interception, and search, and may be disclosed or used for any USG-authorized purpose.
-This IS includes security measures (e.g., authentication and access controls) to protect USG interests -- not for your personal benefit or privacy.
-Notwithstanding the above, using this IS does not constitute consent to PM, LE or CI investigative searching or monitoring of the content of privileged communications, or work product, related to personal representation or services by attorneys, psychotherapists, or clergy, and their assistants. Such communications and work product are private and confidential. See User Agreement for details.
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_banner_etc_issue">CCE-27303-7 (Medium)</a></code>

#### Comments

Personally, I use shorter banners on my systems (from [Cisco Security Baseline](https://www.cisco.com/c/en/us/td/docs/solutions/Enterprise/Security/Baseline_Security/securebasebook/appendxA.html)):

```
UNAUTHORIZED ACCESS TO THIS DEVICE IS PROHIBITED
 You must have explicit, authorized permission to access or configure this device.
 Unauthorized attempts and actions to access or use this system may result in civil and/or 
criminal penalties.
 All activities performed on this device are logged and monitored.
```

#### Useful resources

- [The real purpose of login banners (on Linux)](https://linux-audit.com/the-real-purpose-of-login-banners-on-linux/)
- [Protect SSH Logins with SSH & MOTD Banner Messages](https://www.tecmint.com/protect-ssh-logins-with-ssh-motd-banner-messages/)

### Passwords policy

#### Rationale

Conventionally, Unix shell accounts are accessed by providing a username and password to a login program, which tests these values for correctness using the `/etc/passwd` and `/etc/shadow` files. Password-based login is vulnerable to guessing of weak passwords, and to sniffing and man-in-the-middle attacks against passwords entered over a network or at an insecure console.

- setting the password warning age enables users to make the change at a practical time

  > The DoD requirement is 7 for password warning age. The C2S/CIS profile requirement is 7.

- enforcing a minimum password lifetime helps to prevent repeated password changes to defeat the password reuse

  > The DoD requirement is 1 for password minimum age. The C2S/CIS profile requirement is 7.

- setting the password maximum age ensures users are required to periodically change their passwords

  > The DoD requirement is 60 for password maximum age. The C2S/CIS profile requirement is 90.

#### Solution

###### Set password expiration

```bash
# C2S/CIS: CCE-26486-1 (unknown), CCE-27002-5 (Medium), CCE-27051-2 (Medium)

# Edit /etc/login.defs and set password warning age:
PASS_WARN_AGE 7

# Edit /etc/login.defs and set password minimum age:
PASS_MIN_DAYS 7

# Edit /etc/login.defs and set password maximum age:
PASS_MAX_DAYS 90
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_warn_age_login_defs">CCE-26486-1 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_warn_age_login_defs">CCE-27002-5 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_warn_age_login_defs">CCE-27051-2 (Medium)</a></code>

#### Comments

You can also use following command to set passwords aging for specific user:

```bash
chage -M 180 -m 7 -W 7 <username>
```

#### Useful resources

- [Password Aging](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Security_Guide/s3-wstation-pass-org-age.html) <sup>[Official]</sup>