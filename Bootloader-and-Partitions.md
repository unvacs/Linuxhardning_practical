You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

### Table of Contents

- **[Bootloader](#bootloader)**
  * [Protect bootloader with password](#protect-bootloader-with-password)
  * [Protect bootloader config files](#protect-bootloader-config-files)

- **[Disk Partitioning](#disk-partitioning)**
  * [Separate disk partitions](#separate-disk-partitions)
  * [Restrict mount options](#restrict-mount-options)

### Bootloader

Protection for the boot loader can prevent unauthorized users who have physical access to the system, e.g. attaining root privileges through single user mode.

Basically when you want to prohibit unauthorized reconfiguring of your system, otherwise anybody could load anything on it.

#### Protect bootloader with password

<sup>PCI-DSS | <a href="https://static.open-scap.org/ssg-guides/ssg-rhel7-guide-C2S.html#xccdf_org.ssgproject.content_rule_grub2_password">C2S/CIS (High)</a></sup>

You can set password for the bootloader for prevents users from entering single user mode, changing settings at boot time, access to the bootloader console, reset the root password, if there is no password for GRUB-menu or access to non-secure operating systems.

##### Generate password hash

```bash
# Debian like distributions
grub-mkpasswd-pbkdf2

# RedHat like distributions
grub2-mkpasswd-pbkdf2
```

##### Updated grub configuration

```bash
cat > /etc/grub.d/01_hash << __EOF__
set superusers="user"
password_pbkdf2 user
grub.pbkdf2.sha512.<hash> # rest of your password hash
__EOF__
```

And regenerate grub configuration:

```bash
# Debian like distributions
grub-mkconfig > /boot/grub/grub.cfg

# RedHat like distributions
grub2-mkconfig > /boot/grub2/grub.cfg
```

##### Useful resources

- [How To Password Protect GRUB Bootloader In Linux](https://www.ostechnix.com/password-protect-grub-bootloader-linux/)