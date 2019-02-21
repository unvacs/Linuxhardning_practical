### Welcome to The Practical Linux Hardening Guide wiki! 

You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

**The Practical Linux Hardening Guide** provides a high-level overview of the hardening GNU/Linux systems. It is not an official standard or handbook but it _touches_ and _use_ industry standards.

### Table of Contents

- **[External resources](#external-resources)**
  * [Other official hardening guides](lib/external_resources/other_official_hardening_guides.md#other-official-hardening-guides)
- **[Pre install tasks](#pre-install-tasks)**
  * **[Physical system security](lib/pre_install_tasks/physical_system_security.md#physical-system-security)**
    + [Introduction](lib/pre_install_tasks/physical_system_security.md#information_source-introduction)
    + [Secure rooms](lib/pre_install_tasks/physical_system_security.md#eight_pointed_black_star-secure-rooms)
    + [Monitoring](lib/pre_install_tasks/physical_system_security.md#eight_pointed_black_star-monitoring)
    + [Air conditioning](lib/pre_install_tasks/physical_system_security.md#eight_pointed_black_star-air-conditioning)
    + [Fire protection](lib/pre_install_tasks/physical_system_security.md#eight_pointed_black_star-fire-protection)
    + [Locked racks](lib/pre_install_tasks/physical_system_security.md#eight_pointed_black_star-locked-racks)
    + [Console security](lib/pre_install_tasks/physical_system_security.md#eight_pointed_black_star-console-security)
    + [BIOS protection](lib/pre_install_tasks/physical_system_security.md#eight_pointed_black_star-bios-protection)
    + [Summary checklist](lib/pre_install_tasks/physical_system_security.md#ballot_box_with_check-summary-checklist)
  * **[Hard disk encryption](lib/pre_install_tasks/hard_disk_encryption.md#hard-disk-encryption)**
    + [Introduction](lib/pre_install_tasks/hard_disk_encryption.md#information_source-introduction)
    + [Encrypt root filesystem](lib/pre_install_tasks/hard_disk_encryption.md#eight_pointed_black_star-encrypt-root-filesystem)
    + [Encrypt /boot partition](lib/pre_install_tasks/hard_disk_encryption.md#eight_pointed_black_star-encrypt-boot-partition)
    + [Swap partition](lib/pre_install_tasks/hard_disk_encryption.md#eight_pointed_black_star-swap-partition)
    + [Summary checklist](lib/pre_install_tasks/hard_disk_encryption.md#ballot_box_with_check-summary-checklist)
- **[Post install tasks](#post-install-tasks)**
  * **[Bootloader configuration (grub)](lib/post_install_tasks/bootloader_configuration.md#bootloader-configuration-grub)**
    + [Introduction](lib/post_install_tasks/bootloader_configuration.md#information_source-introduction)
    + [Protect bootloader with password](lib/post_install_tasks/bootloader_configuration.md#eight_pointed_black_star-protect-bootloader-with-password)
    + [Protect bootloader config files](lib/post_install_tasks/bootloader_configuration.md#eight_pointed_black_star-protect-bootloader-config-files)
    + [Summary checklist](lib/post_install_tasks/bootloader_configuration.md#ballot_box_with_check-summary-checklist)
  * **[Disk partitions](lib/post_install_tasks/disk_partitions.md#disk-partitions)**
    + [Introduction](lib/post_install_tasks/disk_partitions.md#information_source-introduction)
    + [Separate disk partitions](lib/post_install_tasks/disk_partitions.md#eight_pointed_black_star-separate-disk-partitions)
    + [Mount options: nodev, noexec and nosuid](lib/post_install_tasks/disk_partitions.md#eight_pointed_black_star-mount-options-nodev-nosuid-and-noexec)
    + [Secure /boot directory](lib/post_install_tasks/disk_partitions.md#eight_pointed_black_star-secure-boot-directory)
    + [Secure /tmp and /var/tmp](lib/post_install_tasks/disk_partitions.md#eight_pointed_black_star-secure-tmp-and-vartmp)
    + [Secure /dev/shm](lib/post_install_tasks/disk_partitions.md#eight_pointed_black_star-secure-devshm)
    + [Secure /proc filesystem](lib/post_install_tasks/disk_partitions.md#eight_pointed_black_star-secure-proc-filesystem)
    + [Swap partition](lib/post_install_tasks/disk_partitions.md#eight_pointed_black_star-swap-partition)
    + [Disk quotas](lib/post_install_tasks/disk_partitions.md#eight_pointed_black_star-disk-quotas)
    + [Summary checklist](lib/post_install_tasks/disk_partitions.md#ballot_box_with_check-summary-checklist)
  * **[Keep system updated](lib/post_install_tasks/keep_system_updated.md#keep-system-updated)**
    + [Introduction](lib/post_install_tasks/keep_system_updated.md#information_source-introduction)
    + [Make sure that the system is up to date](lib/post_install_tasks/keep_system_updated.md#eight_pointed_black_star-make-sure-that-the-system-is-up-to-date)
    + [Automatic security updates](lib/post_install_tasks/keep_system_updated.md#eight_pointed_black_star-automatic-security-updates)
    + [Useful resources](lib/post_install_tasks/keep_system_updated.md#link-useful-resource)
    + [Summary checklist](lib/post_install_tasks/keep_system_updated.md#ballot_box_with_check-summary-checklist)
  * **[Package management](#package-management)**
    + [Introduction](lib/post_install_tasks/package_management.md#information_source-introduction)
    + [Check package signatures](lib/post_install_tasks/package_management.md#information_source-introduction)
    + [Remove packages with known issues](lib/post_install_tasks/package_management.md#remove-packages-with-known-issues)
    + [Useful resources](lib/post_install_tasks/package_management.md#link-useful-resource)
    + [Summary checklist](lib/post_install_tasks/package_management.md#ballot_box_with_check-summary-checklist)
  * **[Netfilter ruleset](#netfilter-ruleset)**
    + [Shorewall](#shorewall)
  * **[TCP wrapper](#tcp-wrapper)**
  * **[Users and groups](#users-and-groups)**
    + [Limit su access](#limit-su-access)
    + [Disable root account](#disable-root-account)
    + [Logins to system console](#logins-to-system-console)
    + [Disable shell accounts](#disable-shell-accounts)
    + [Strong password policy](#strong-password-policy)
    + [Password aging](#password-aging)
    + [Previous passwords](#previous-passwords)
    + [Login failures](#login-failures)
    + [Protect single user mode](#protect-single-user-mode)
  * **[System path permissions](#system-path-permissions)**
    + [World writable files](#world-writable-files)
  * **[PAM module](#pam-module)**
  * **[Limits](#limits)**
  * **[Shadow passwords](#shadow-passwords)**
  * **[Linux kernel hardening](#linux-kernel-hardening)**
    + [Kernel parameters](#kernel-parameters)
      + [Network security](#improve-network-security)
      + [System security](#improve-system-security)
  * **[Remove unused modules](#remove-unused-modules)**
  * **[Secure shared memory](#secure-shared-memory)**
  * **[IRQ balance](#irq-balance)**
  * **[Disable compilers](#disable-compilers)**
  * **[Email notifications](#email-notifications)**
    + [Rebooting the system](#rebooting-the-system)
    + [Login the system](#login-the-system)
  * **[Backups](#backups)**
    + [Backup policy](#backup-policy)
  * **[External devices](#external-devices)**
    + [Disable USB usage](#disable-usb-usage)
- **[Tools](#tools)**
  * **[Logging and Auditing](#logging-and-auditing)**
    + [Syslog](#syslog)
    + [Auditd](#auditd)
    + [OSSEC](#ossec)
    + [Tiger](#tiger)
    + [Aide](#aide)
    + [Logwatch](#logwatch)
  * **[SELinux](#selinux)**
  * **[Other](#other)**
    + [Fail2ban](#fail2ban)
    + [PSAD](#psad)
    + [Entropy daemon](#entropy-daemon)
    + [Centralized authentication service](#centralized-authentication-service)
  * **[Testing tools](#testing-tools)**
    + [Lynis](#lynis)
    + [Chrootkit](#chrootkit)
- **[Services](#services)**
  * **[Disable all unnecessary services](lib/services/disable_all_unnecessary_services.md#disable-all-unnecessary-services)**
    + [Common unix print system](lib/services/disable_all_unnecessary_services.md#eight_pointed_black_star-common-unix-print-system)
    + [Summary checklist](lib/services/disable_all_unnecessary_services.md#ballot_box_with_check-summary-checklist)
  * **[System services](#system-services)**
    + [OpenSSH](#openssh)
    + [NTP](#ntp)
    + [Cron](#cron)
    + [Anacron](#anacron)
    + [GnuPG 2](#gnupg2)
      + [Unattended key generation](#unattended-key-generation)
  * **[DNS services](#dns-services)**
    + [Bind9](#bind9)
    + [Unbound](#unbound)
    + [Knot Resolver](#knot-resolver)
  * **[Mail services](#mail-services)**
    + [Postfix](#postfix)
  * **[Web services](lib/services/web_services.md#web-services)**
    + [Nginx](lib/services/web_services.md#nginx)
      - [Files and directories permissions](lib/services/web_services.md#eight_pointed_black_star-files-and-directories-permissions)
      - [Use HTTPS](lib/services/web_services.md#eight_pointed_black_star-use-https)
      - [Enable HTTP2](lib/services/web_services.md#eight_pointed_black_star-enable-http2)
      - [Separate domains](lib/services/web_services.md#eight_pointed_black_star-separate-domains)
      - [Redirect all unencrypted traffic to HTTPS](lib/services/web_services.md#eight_pointed_black_star-redirect-all-unencrypted-traffic-to-https)
      - [Enable HTTP Strict Transport Security](lib/services/web_services.md#eight_pointed_black_star-enable-http-strict-transport-security)
      - [Diffie Hellman Ephemeral Parameter](lib/services/web_services.md#eight_pointed_black_star-diffie-hellman-ephemeral-parameter)
      - [Security related headers](lib/services/web_services.md#eight_pointed_black_star-security-related-headers)
    + [Apache](#apache)
  * **[Databases](#databases)**
    + [PostgreSQL](#postgresql)
    + [MySQL](#mysql)
    + [Redis](#redis)
  * **[Queues](#queues)**
    + [AMQP](#amqp)
- **[Containers](#containers)**
  * **[LXC/LXD](#lxc-lxd)**
  * **[Docker](#docker)**
  * **[Hashicorp suite](#hashicorp-suite)**
- **[Deployment](#deployment)**
- **[Testing configuration](#testing-configuration)**