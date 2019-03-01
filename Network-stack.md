You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Network stack](#network-stack)**
  * [IPv6 redirects](#ipv6-redirects)
  * [IPv6 router advertisements](#ipv6-router-advertisements)
  * [IPv6 support automatic loading](#ipv6-support-automatic-loading)
  * [Source-routed packets](#source-routed-packets)
  * [Ignore bogus ICMP error responses](#ignore-bogus-icmp-error-responses)
  * [Accepting ICMP redirects](#accepting-icmp-redirects)
  * [Use reverse path filtering](#use-reverse-path-filtering)
  * [Accepting secure redirects](#accepting-secure-redirects)
  * [TCP Syncookies](#tcp-syncookies)
  * [Log Martian packets](#log-martian-packets)
  * [ICMP broadcast echo requests](#icmp-broadcast-echo-requests)
  * [IP forwarding](#ip-forwarding)
  * [Sending ICMP redirects](#sending-icmp-redirects)
  * [Keep sockets in FIN-WAIT-2 state](#keep-sockets-in-fin-wait-2-state)
  * [Keepalive packets to keep an connection alive](#keepalive-packets-to-keep-an-connection-alive)

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

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.all.accept_source_route = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_all_accept_source_route">C2S/CIS: CCE-27434-0 (Medium)</a></sup>

#### Useful resources

- []()

### Ignore bogus ICMP error responses

#### Rationale

Ignoring bogus ICMP error responses reduces log size, although some activity would not be logged.

#### Solution

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.icmp_ignore_bogus_error_responses = 1
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_icmp_ignore_bogus_error_responses">C2S/CIS: CCE-80166-2 (Unknown)</a></sup>

#### Useful resources

- []()

### Accepting ICMP redirects

#### Rationale

ICMP redirect messages are used by routers to inform hosts that a more direct route exists for a particular destination. These messages modify the host's route table and are unauthenticated.

#### Solution

###### By default

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.default.accept_redirects = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_default_accept_redirects">C2S/CIS: CCE-80158-9 (Medium)</a></sup>

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.all.accept_redirects = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_all_accept_redirects">C2S/CIS: CCE-80163-9 (Medium)</a></sup>

#### Useful resources

- []()

### Use reverse path filtering

#### Rationale

Enabling reverse path filtering drops packets with source addresses that should not have been able to be received on the interface they were received on.

#### Solution

###### By default

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.default.rp_filter = 1
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_default_rp_filter">C2S/CIS: CCE-80168-8 (Medium)</a></sup>

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.all.rp_filter = 1
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_all_rp_filter">C2S/CIS: CCE-80167-0 (Medium)</a></sup>

#### Useful resources

- []()

### Accepting secure redirects

#### Rationale

Accepting "secure" ICMP redirects (from those gateways listed as default gateways) has few legitimate uses. It should be disabled unless it is absolutely required.

#### Solution

###### By default

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.default.secure_redirects = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_default_secure_redirects">C2S/CIS: CCE-80164-7 (Medium)</a></sup>

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.all.secure_redirects = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_all_secure_redirects">C2S/CIS: CCE-80159-7 (Medium)</a></sup>

#### Useful resources

- []()

### TCP Syncookies

#### Rationale

A TCP SYN flood attack can cause a denial of service by filling a system's TCP connection table with connections in the SYN_RCVD state.

This feature is activated when a flood condition is detected, and enables the system to continue servicing valid connection requests.

#### Solution

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.all.accept_source_route = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_tcp_syncookies">C2S/CIS: CCE-27495-1 (Medium)</a></sup>

#### Useful resources

- []()

### Log Martian packets

#### Rationale

The presence of "martian" packets (which have impossible addresses) as well as spoofed packets, source-routed packets, and redirects could be a sign of nefarious network activity. Logging these packets enables this activity to be detected.

#### Solution

###### By default

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.default.log_martians = 1
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_default_log_martians">C2S/CIS: CCE-80161-3 (Unknown)</a></sup>

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.all.log_martians = 1
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_all_log_martians">C2S/CIS: CCE-80160-5 (Unknown)</a></sup>

#### Useful resources

- []()

### ICMP broadcast echo requests

#### Rationale

Responding to broadcast (ICMP) echoes facilitates network mapping and provides a vector for amplification attacks.

Ignoring ICMP echo requests (pings) sent to broadcast or multicast addresses makes the system slightly more difficult to enumerate on the network.

#### Solution

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.icmp_echo_ignore_broadcasts = 1
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_icmp_echo_ignore_broadcasts">C2S/CIS: CCE-80165-4 (Unknown)</a></sup>

#### Useful resources

- []()

### IP forwarding

#### Rationale

Routing protocol daemons are typically used on routers to exchange network topology information with other routers. If this capability is used when not required, system network information may be unnecessarily transmitted across the network.

#### Solution

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.ip_forward = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_ip_forward">C2S/CIS: CCE-80157-1 (Medium)</a></sup>

#### Useful resources

- []()

### Sending ICMP redirects

#### Rationale

ICMP redirect messages are used by routers to inform hosts that a more direct route exists for a particular destination. These messages contain information from the system's route table possibly revealing portions of the network topology.

#### Solution

###### By default

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.default.send_redirects = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_default_send_redirects">C2S/CIS: CCE-80156-3 (Medium)</a></sup>

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.conf.all.send_redirects = 0
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_sysctl_net_ipv4_conf_all_send_redirects">C2S/CIS: CCE-80156-3 (Medium)</a></sup>

#### Useful resources

- []()

### Keep sockets in FIN-WAIT-2 state

  > Not available from C2S/CIS standard.

#### Rationale

The connection is being kept around so that any delayed packets can be matched to the connection and handled appropriately.

Decreasing this value can avoid some DDoS attacks or other problems (e.g. memory consuming) that arose from getting huge amounts of connections.

#### Solution

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.tcp_fin_timeout = 30
```

<sup><a href="https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt">Sysctl Documentation</a></sup>

#### Comments

If you set too large value to `tcp_fin_timeout`, the system may become out of port, file-descripter and memory. If you set too small value, the system may leak delayed packets.

#### Useful resources

- []()

### Keepalive packets to keep an connection alive

  > Not available from C2S/CIS standard.

#### Rationale

TCP keepalive keeps the connection open in case an error has happened. This kernel feature ensures that a TCP connection will be kept active by simulating traffic on it so it is not marked by the communication layer as inactive.

#### Solution

###### On all interfaces

```bash
# Add to /etc/sysctl.d/network-stack.conf
net.ipv4.tcp_keepalive_time = 180
net.ipv4.tcp_keepalive_intvl = 10
net.ipv4.tcp_keepalive_probes = 3
```

<sup><a href="https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt">Sysctl Documentation</a></sup>

#### Comments

Remember that keepalive support, even if configured in the kernel, is not the default behavior in Linux. Programs must request keepalive control for their sockets using the setsockopt interface.

High values can be especially harmful for expensive connections such as database connections.

#### Useful resources

- [Custom Configuration of TCP Socket Keep-Alive Timeouts](http://coryklein.com/tcp/2015/11/25/custom-configuration-of-tcp-socket-keep-alive-timeouts.html)
