You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Maintaining Software](#keep-system-updated)**
  * [Keep system updated](#keep-system-updated)
  * [Package management](#package-management)

## Maintaining Software

### Keep system updated

#### Rationale

Software updates offer plenty of benefits. It’s all about revisions. These might include repairing security holes that have been discovered and fixing or removing bugs.

Some benefits:

- close up problems of security that has been discovered
- it can improve the stability of the system
- improvements the system stacks or network stacks

#### Solution

###### Update system with yum

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

Also these rules are important:

##### Check for updates
```bash
yum check-update
```

##### Install upgrades (with security updates)

```bash
yum --security upgrade
```

#### Useful resources

- [Yum](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/ch-yum) <sup>[Official]</sup>
- [In CentOS, what is the difference between yum update and yum upgrade?](https://unix.stackexchange.com/questions/55777/in-centos-what-is-the-difference-between-yum-update-and-yum-upgrade)
