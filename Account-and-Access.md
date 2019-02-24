You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Accounts and Access](#accounts-and-access)**
  * [Physical console access](#)
  * [Session configuration files](#)
  * [Banners](#)
  * [Passwords policy](#)

## Accounts and Access

In traditional Unix security, if an attacker gains shell access to a certain login account, they can perform any action or access any file to which that account has access.

Therefore, making it more difficult for unauthorized people to gain shell access to accounts, particularly to privileged accounts, is a necessary part of securing a system.

### Physical console access

#### Rationale

There are some steps which, if taken, make it more difficult for an attacker to quickly or undetectably modify a system from its console.

This is the easiest way to gain unauthorised access to a Linux system is to boot the server into single user mode. Attacker can select a kernel to boot from the grub menu item by pressing specific key to edit the boot option.

  > Remember to protect GRUB with password because it's the only way to protect single user mode in RedHat/CentOS distributions.

#### Solution

###### Set authentication for single user mode

```bash
# C2S/CIS: CCE-27287-2 (Medium)

```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_require_singleuser_auth">CCE-27287-2 (Medium)</a></code>

#### Comments

##### asd



#### Useful resources

- []()
