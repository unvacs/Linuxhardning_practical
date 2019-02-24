You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Maintaining Software](#maintaining-software)**
  * [Package signatures](#package-signatures)
  * [Keep system updated](#keep-system-updated)
  * [Remove unused packages](#remove-unused-packages)

## Maintaining Software

Software maintenance is extremely important to maintaining a secure system. It is vital to patch software as soon as it becomes available in order to prevent attackers from using known holes to infiltrate your system.

#### Useful resources

- [Software Maintenance](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/security_guide/chap-security_guide-software_maintenance) <sup>[Official]</sup>

### Package signatures

#### Rationale

Changes to any software components can have significant effects on the overall security of the operating system. This requirement ensures the software has not been tampered with and that it has been provided by a trusted vendor. 

#### Solution

###### Enabled gpgcheck option

```bash
# C2S/CIS: CCE-26989-4 (High)

gpgcheck=1
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_ensure_gpgcheck_globally_activated">CCE-26989-4 (High)</a></code>

#### Useful resources

- [Configuring Yum and Yum Repositories](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/sec-configuring_yum_and_yum_repositories) <sup>[Official]</sup>

### Keep system updated

#### Rationale

Software updates offer plenty of benefits. It’s all about revisions. These might include repairing security holes that have been discovered and fixing or removing bugs.

  > U.S. Defense systems are required to be patched within 30 days or sooner as local policy dictates.

Some benefits:

- close up problems of security that has been discovered
- it can improve the stability of the system
- improvements the system stacks or network stacks

#### Solution

###### Updating all packages and dependencies

```bash
# C2S/CIS: CCE-26895-3 (High)

yum update
```

#### Policies

<code>C2S/CIS: <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_security_patches_up_to_date">CCE-26895-3 (High)</a></code>

#### Comments

Before updating the system, I do it in the console:

```bash
# This one-liner save the update process session:
script -t 2>~/upgrade.time -a ~/upgrade.script
```

Also these one-liners are important:

###### Check for updates

```bash
yum check-update
```

###### Install upgrades (with security updates)

```bash
yum --security upgrade
```

###### Roll back an update

```bash
yum history undo <id>
```

#### Useful resources

- [Yum](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/ch-yum) <sup>[Official]</sup>
- [How to use yum history to roll back an update in Red Hat Enterprise Linux 6 , 7?](https://access.redhat.com/solutions/64069) <sup>[Official]</sup>
- [In CentOS, what is the difference between yum update and yum upgrade?](https://unix.stackexchange.com/questions/55777/in-centos-what-is-the-difference-between-yum-update-and-yum-upgrade)
