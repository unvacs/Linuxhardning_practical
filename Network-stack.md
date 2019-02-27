You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Network stack](#network-stack)**
  * [IPv6 redirects](#ipv6-redirects)
  * [IPv6 router advertisements](#ipv6-router-advertisements)
  * [IPv6 support automatic loading](#ipv6-support-automatic-loading)
  * [Source-routed packets](#source-routed-packets)

## Network stack

### IPv6 redirects

#### Rationale

An illicit ICMP redirect message could result in a man-in-the-middle attack.

#### Solution

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv6.conf.all.accept_redirects = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv6_conf_default_accept_redirects">C2S/CIS: CCE-80183-7 (Medium)</a></sup>

###### By default

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv6.conf.default.accept_redirects = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv6_conf_default_accept_ra">C2S/CIS: CCE-80181-1 (Unknown)</a></sup>

#### Useful resources

- []()

### IPv6 router advertisements

#### Rationale

An illicit router advertisement message could result in a man-in-the-middle attack.

#### Solution

###### By default

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv6.conf.default.accept_ra = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv6_conf_default_accept_ra">C2S/CIS: CCE-80181-1 (Unknown)</a></sup>

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv6.conf.all.accept_ra = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv6_conf_all_accept_ra">C2S/CIS: CCE-80180-3 (Unknown)</a></sup>

#### Useful resources

- []()

### IPv6 support automatic loading

#### Rationale

Any unnecessary network stacks - including IPv6 - should be disabled, to reduce the vulnerability to exploitation.

#### Solution

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv6.conf.all.disable_ipv6 = 1
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv6_conf_all_disable_ipv6">C2S/CIS: CCE-80175-3 (Medium)</a></sup>

#### Useful resources

- []()

### Source-routed packets

#### Rationale

Source-routed packets allow the source of the packet to suggest routers forward the packet along a different path than configured on the router, which can be used to bypass network security measures.

#### Solution

###### By default

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.default.accept_source_route = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_default_accept_source_route">C2S/CIS: CCE-80162-1 (Medium)</a></sup>

#### Useful resources

- []()
