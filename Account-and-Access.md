You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Accounts and Access](#accounts-and-access)**
  * [Physical console access](#physical-console-access)
  * [Session configuration files](#session-configuration-files)
  * [Login banners](#login-banners)
  * [Passwords policy](#passwords-policy)
  * [Restrict root logins](#restrict-root-logins)

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
# Edit /usr/lib/systemd/system/rescue.service:
ExecStart=-/bin/sh -c "/usr/sbin/sulogin; /usr/bin/systemctl --fail --no-block default"
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_require_singleuser_auth">C2S/CIS: CCE-27287-2 (Medium)</a></sup>

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
# Edit /etc/profile and /etc/bashrc:
umask 027
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_umask_etc_bashrc">C2S/CIS: CCE-80202-5 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_umask_etc_profile">C2S/CIS: CCE-80204-1 (unknown)</a></sup>

#### Comments

I have seen recommendations in some guides (and in the real configurations) to set the `umask 077` value. Many of Linux distributions are shipped by default with `umask 022`.

`umask 027` is better from security perspective. `umask 077` is even better to use for root (`077` means that noone but the owner is able to read or execute newly-created files).

I'm sure there's a perfectly rational explanation why it is used:

- it avoids some common system administrator mistakes
- it's harder for an attacker to run privilege escalation

The most obvious downside is when you start creating files/directories in a shared directory, expecting other users to access them.

#### Useful resources

- [Best practices for umask in Red Hat Enterprise Linux](https://access.redhat.com/solutions/107683) <sup>[Official]</sup>

### Login banners

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

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_banner_etc_issue">C2S/CIS: CCE-27303-7 (Medium)</a></sup>

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

  > The DoD requirement is 7. The C2S/CIS profile requirement is 7.

- enforcing a minimum password lifetime helps to prevent repeated password changes to defeat the password reuse

  > The DoD requirement is 1. The C2S/CIS profile requirement is 7.

- setting the password maximum age ensures users are required to periodically change their passwords

  > The DoD requirement is 60. The C2S/CIS profile requirement is 90.

Disabling inactive accounts ensures that accounts which may not have been responsibly removed are not available to attackers who may have compromised their credentials.

#### Solution

###### Set password expiration

```bash
# Edit /etc/login.defs and set password warning age:
PASS_WARN_AGE 7

# Edit /etc/login.defs and set password minimum age:
PASS_MIN_DAYS 7

# Edit /etc/login.defs and set password maximum age:
PASS_MAX_DAYS 90
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_warn_age_login_defs">C2S/CIS: CCE-26486-1 (unknown)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_warn_age_login_defs">C2S/CIS: CCE-27002-5 (Medium)</a>; <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_password_warn_age_login_defs">C2S/CIS: CCE-27051-2 (Medium)</a></sup>

###### Set account expiration

```bash
# Edit /etc/default/useradd:
INACTIVE=30
```

#### Policies

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_account_disable_post_pw_expiration">C2S/CIS: CCE-27355-7 (Medium)</a></sup>

#### Comments

You can also use following command to set password aging and account expiration for specific user:

```bash
chage -M 90 -m 7 -W 7 -I 30 <username>
```

#### Useful resources

- [Password Aging](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Security_Guide/s3-wstation-pass-org-age.html) <sup>[Official]</sup>

### Restrict root logins

#### Rationale

Direct root logins should be allowed only for emergency use. In normal situations, the administrator should access the system via a unique unprivileged account, and then use `su` or `sudo` to execute privileged commands.

#### Solution

###### Verify only root has UID 0

Multiple accounts with a UID of 0 afford more opportunity for potential intruders to guess a password for a privileged account.

```bash
awk -F: '$3 == 0 && $1 != "root" { print $1 }' /etc/passwd | xargs passwd -l
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_accounts_no_uid_except_zero">C2S/CIS: CCE-27175-9 (High)</a></sup>

###### Protect direct root logins

Disabling direct root logins ensures proper accountability and multifactor authentication to privileged accounts.

```bash
echo > /etc/securetty
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_no_direct_root_logins">C2S/CIS: CCE-27294-8 (Medium)</a></sup>

###### Protect direct root logins

Ensuring shells are not given to system accounts upon login makes it more difficult for attackers to make use of system accounts.

  > Do not perform the steps in this section on the root account.

```bash
usermod -s /sbin/nologin SYSACCT
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_no_shelllogin_for_systemaccounts">C2S/CIS: CCE-26448-1 (Medium)</a></sup>

#### Comments

A blank `/etc/securetty` file does not prevent the root user from logging in remotely using the OpenSSH suite of tools because the console is not opened until after authentication.

#### Useful resources

- [Disallowing Root Access](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Security_Guide/s2-wstation-privileges-noroot.html) <sup>[Official]</sup>
