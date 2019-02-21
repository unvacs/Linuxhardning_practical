## Bootloader and Partitions

You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

### Table of Contents
- [Bootloader configuration (grub)](https://github.com/trimstray/the-practical-linux-hardening-guide/wiki/Bootloader-and-Partitions#bootloader-configuration-grub)
  * [Introduction](#introduction)
  * [Protect bootloader with password](https://github.com/trimstray/the-practical-linux-hardening-guide/wiki/Bootloader-and-Partitions#protect-bootloader-with-password)
    + [Generate password hash](https://github.com/trimstray/the-practical-linux-hardening-guide/wiki/Bootloader-and-Partitions#generate-password-hash)
    + [Updated grub configuration](https://github.com/trimstray/the-practical-linux-hardening-guide/wiki/Bootloader-and-Partitions#updated-grub-configuration)
  * [Useful resources](https://github.com/trimstray/the-practical-linux-hardening-guide/wiki/Bootloader-and-Partitions#useful-resources)
  * [Protect bootloader config files](https://github.com/trimstray/the-practical-linux-hardening-guide/wiki/Bootloader-and-Partitions#protect-bootloader-config-files)
  * [Summary checklist](https://github.com/trimstray/the-practical-linux-hardening-guide/wiki/Bootloader-and-Partitions#summary-checklist)

### Bootloader configuration (grub)

#### Introduction

Protection for the boot loader can prevent unauthorized users who have physical access to systems, e.g. attaining root privileges through single user mode.

Basically when you want to prohibit unauthorized reconfiguring of your system, otherwise anybody could load anything on it.

#### Protect bootloader with password

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

#### Useful resources

- [How To Password Protect GRUB Bootloader In Linux](https://www.ostechnix.com/password-protect-grub-bootloader-linux/)

#### Protect bootloader config files

Set the owner and group of `/etc/grub.conf` to the root user:

```bash
chown root:root /etc/grub.conf
```

or

```bash
chown -R root:root /etc/grub.d
```

Set permission on the `/etc/grub.conf` or `/etc/grub.d` file to read and write for root only:

```bash
chmod og-rwx /etc/grub.conf
```

or

```bash
chmod -R og-rwx /etc/grub.d
```

#### Summary checklist

| <b>Item</b> | <b>True</b> | <b>False</b> |
| :---        | :---:       | :---:        |
| Set password for the bootloader | :black_square_button: | :black_square_button: |
