You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Maintaining Software](#maintaining-software)**
  * [Package signatures](#package-signatures)
  * [Keep system updated](#keep-system-updated)
  * [Disable vulnerable software](#disable-vulnerable-software)
  * [Enable important software](#enable-important-software)

## Maintaining Software

Software mintenance is extremely important to maintaining a secure system. It is vital to patch software as soon as it becomes available in order to prevent attackers from using known holes to infiltrate your system.

### Package signatures

#### Rationale

Changes to any software components can have significant effects on the overall security of the operating system. This requirement ensures the software has not been tampered with and that it has been provided by a trusted vendor.

#### Solution

###### Enabled gpgcheck option

```bash
gpgcheck=1
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_ensure_gpgcheck_globally_activated">C2S/CIS: CCE-26989-4 (High)</a></sup>

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
yum update
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_security_patches_up_to_date">C2S/CIS: CCE-26895-3 (High)</a></sup>

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

### Disable vulnerable software

#### Rationale

The best protection against vulnerable software is running less software.

#### Solution

###### Remove or disable unnecessary services

  > From C2S/CIS: _These legacy clients contain numerous security exposures and have been replaced with the more secure SSH package. Removing the rsh package removes the clients for rsh,rcp, and rlogin._

```bash
yum remove rsh
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_group_r_services">C2S/CIS: CCE-27274-0 (unknown)</a></sup>

  > From C2S/CIS: _The rlogin service uses unencrypted network communications, which means that data from the login session, including passwords and all other information transmitted during the session, can be stolen by eavesdroppers on the network._

```bash
systemctl disable rlogin.socket
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_rlogin_disabled">C2S/CIS: CCE-27336-7 (High)</a></sup>

  > From C2S/CIS: _The rexec service uses unencrypted network communications, which means that data from the login session, including passwords and all other information transmitted during the session, can be stolen by eavesdroppers on the network._

```bash
systemctl disable rexec.socket
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_rexec_disabled">C2S/CIS: CCE-27408-4 (High)</a></sup>

  > From C2S/CIS: _The rsh service uses unencrypted network communications, which means that data from the login session, including passwords and all other information transmitted during the session, can be stolen by eavesdroppers on the network._

```bash
systemctl disable rsh.socket
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_rsh_disabled">C2S/CIS: CCE-27337-5 (High)</a></sup>

  > From C2S/CIS: _Trust files are convenient, but when used in conjunction with the R-services, they can allow unauthenticated access to a system._

```bash
rm /etc/hosts.equiv
rm ~/.rhosts
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_no_rsh_trust_files">C2S/CIS: CCE-27406-8 (High)</a></sup>

  > From C2S/CIS: _The telnet protocol uses unencrypted network communication, which means that data from the login session, including passwords and all other information transmitted during the session, can be stolen by eavesdroppers on the network._

```bash
# Edit /etc/xinetd.d/telnet:
disable = yes
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_telnet_disabled">C2S/CIS: CCE-27401-9 (High)</a></sup>

  > From C2S/CIS: _The NIS service provides an unencrypted authentication service which does not provide for the confidentiality and integrity of user passwords or the remote session._

```bash
yum erase ypserv
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_package_ypserv_removed">C2S/CIS: CCE-27399-5 (High)</a></sup>

  > From C2S/CIS: _Disabling the tftp service ensures the system is not acting as a TFTP server, which does not provide encryption or authentication._

```bash
systemctl disable tftp.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_tftp_disabled">C2S/CIS: CCE-80212-4 (Medium)</a></sup>

  > From C2S/CIS: _The xinetd service provides a dedicated listener service for some programs, which is no longer necessary for commonly-used network services._

```bash
systemctl disable xinetd.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_xinetd_disabled">C2S/CIS: CCE-27443-1 (Medium)</a></sup>

  > From C2S/CIS: _The talk software presents a security risk as it uses unencrypted protocols for communications._

```bash
yum erase talk
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_package_talk_removed">C2S/CIS: CCE-27432-4 (Medium)</a></sup>

  > From C2S/CIS: _The talk software presents a security risk as it uses unencrypted protocols for communications._

```bash
yum erase talk-server
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_package_talk-server_removed">C2S/CIS: CCE-27210-4 (Medium)</a></sup>

  > From C2S/CIS: _Running FTP server software provides a network-based avenue of attack, and should be disabled if not needed. Furthermore, the FTP protocol is unencrypted and creates a risk of compromising sensitive information._

```bash
systemctl disable vsftpd.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_vsftpd_disabled">C2S/CIS: CCE-80244-7 (Unknown)</a></sup>

  > From C2S/CIS: _X windows has a long history of security vulnerabilities and should not be installed unless approved and documented._

```bash
yum groupremove "X Window System"
yum remove xorg-x11-server-common
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_crond_enabled">C2S/CIS: CCE-27218-7 (Medium)</a></sup>

  > From C2S/CIS: _Because the Avahi daemon service keeps an open network port, it is subject to network attacks. Its functionality is convenient but is only appropriate if the local network can be trusted._

```bash
systemctl disable avahi-daemon.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_avahi-daemon_disabled">C2S/CIS: CCE-80338-7 (Unknown)</a></sup>

#### Comments

The C2S/CIS standard also explains the following services. You should consider which ones are use. If they are not use on the local system then this service should be disabled.

Only reason to have some of these services might be some kind of dependency issue.

  > From C2S/CIS: _Running SNMP software provides a network-based avenue of attack, and should be disabled if not needed._

```bash
systemctl disable snmpd.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_snmpd_disabled">C2S/CIS: CCE-80274-4 (Unknown)</a></sup>

  > From C2S/CIS: _All network services involve some risk of compromise due to implementation flaws and should be disabled if possible._

```bash
systemctl disable named.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_named_disabled">C2S/CIS: CCE-80325-4 (Unknown)</a></sup>

  > From C2S/CIS: _Unnecessary packages should not be installed to decrease the attack surface of the system._

```bash
yum erase openldap-servers
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_package_openldap-servers_removed">C2S/CIS: CCE-80293-4 (Unknown)</a></sup>

  > From C2S/CIS: _Running a Samba server provides a network-based avenue of attack, and should be disabled if not needed._

```bash
systemctl disable smb.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_smb_disabled">C2S/CIS: CCE-80277-7 (Unknown)</a></sup>

  > From C2S/CIS: _Running web server software provides a network-based avenue of attack, and should be disabled if not needed._

```bash
systemctl disable httpd.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_httpd_disabled">C2S/CIS: CCE-80300-7 (Unknown)</a></sup>

  > From C2S/CIS: _Although systems management and patching is extremely important to system security, management by a system outside the enterprise enclave is not desirable for some environments._

```bash
systemctl disable rhnsd.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_rhnsd_disabled">C2S/CIS: CCE-80269-4 (Unknown)</a></sup>

  > From C2S/CIS: _Running proxy server software provides a network-based avenue of attack, and should be removed if not needed._

```bash
systemctl disable squid.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_squid_disabled">C2S/CIS: CCE-80285-0 (Unknown)</a></sup>

  > From C2S/CIS: _Unmanaged or unintentionally activated DHCP servers may provide faulty information to clients, interfering with the operation of a legitimate site DHCP server if there is one._

```bash
systemctl disable dhcpd.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_dhcpd_disabled">C2S/CIS: CCE-80330-4 (Medium)</a></sup>

  > From C2S/CIS: _Running an IMAP or POP3 server provides a network-based avenue of attack, and should be disabled if not needed._

```bash
systemctl disable dovecot.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_dhcpd_disabled">C2S/CIS: CCE-80294-2 (Unknown)</a></sup>

  > From C2S/CIS: _All of these daemons (nfslock, rpcgssd, and rpcidmapd) run with elevated privileges, and many listen for network connections._

```bash
systemctl disable rpcbind.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_rpcbind_disabled">C2S/CIS: CCE-80230-6 (Medium)</a></sup>

  > From C2S/CIS: _Unnecessary services should be disabled to decrease the attack surface of the system._

```bash
systemctl disable nfs.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_nfs_disabled">C2S/CIS: CCE-80237-1 (Unknown)</a></sup>

  > From C2S/CIS: _Turn off unneeded services to reduce attack surface._

```bash
systemctl disable cups.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_cups_disabled">C2S/CIS: CCE-80282-7 (Unknown)</a></sup>

#### Useful resources

- [Which services do we really need?](https://www.tldp.org/HOWTO/Security-Quickstart-HOWTO/services.html)

### Enable important software

#### Rationale

The best protection against vulnerable software is running less software.

#### Solution

###### Install tcp wrappers

  > From C2S/CIS: _Due to its usage for maintenance and security-supporting tasks, enabling the cron daemon is essential._

```bash
systemctl enable crond.service
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_crond_enabled">C2S/CIS: CCE-27323-5 (Medium)</a></sup>

  > From C2S/CIS: _Access control methods provide the ability to enhance system security posture by restricting services and known good IP addresses and address ranges._

```bash
yum install tcp_wrappers
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_package_tcp_wrappers_installed">C2S/CIS: CCE-27361-5 (Medium)</a></sup>

###### Enable chronyd/ntpd

  > From C2S/CIS: _Synchronizing time is essential for authentication services such as Kerberos, but it is also important for maintaining accurate logs and auditing possible security breaches. The chronyd and ntpd NTP daemons offer all of the functionality of ntpdate, which is now deprecated._

```bash
systemctl enable chronyd
# or
systemctl enable ntpd
```

<sup><a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_service_chronyd_or_ntpd_enabled">C2S/CIS: CCE-27444-9 (Medium)</a></sup>

#### Useful resources

- [Comparison of NTP implementations](https://chrony.tuxfamily.org/comparison.html)
