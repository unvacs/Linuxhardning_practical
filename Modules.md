You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Kernel modules](#kernel-modules)**
  * [Prevent kernel modules being loaded](#prevent-kernel-modules-being-loaded)

## Kernel modules

### Prevent kernel modules being loaded

#### Rationale

Although security vulnerabilities in kernel networking code are not frequently discovered, the consequences can be dramatic.

#### Solution

###### Disable DCCP support

Disabling DCCP protects the system against exploitation of any flaws in its implementation.

```bash
# Add to /etc/modprobe.d/modules.conf:
install dccp /bin/true
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_kernel_module_dccp_disabled">C2S/CIS: CCE-26828-4 (Medium)</a></sup>

###### Disable SCTP support

Disabling SCTP protects the system against exploitation of any flaws in its implementation.

```bash
# Add to /etc/modprobe.d/modules.conf:
install sctp /bin/true
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_kernel_module_sctp_disabled">C2S/CIS: CCE-27106-4 (Medium)</a></sup>

#### Comments


#### Useful resources

- []()
