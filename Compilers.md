You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Compilers](#compilers)**
  * [Disable the compilers](#disable-the-compilers)

## Compilers

  > Not available from C2S/CIS standard.

Compilers can be used by hackers from compiling software like worms or root kits to be used on your server.

### Disable the compilers

#### Rationale

This can help you to protect your server from attacks that exploit vulnerabilities in those compilers.

#### Solution

###### For all unprivileged users

```bash
chmod 000 /usr/bin/byacc
chmod 000 /usr/bin/yacc
chmod 000 /usr/bin/bcc
chmod 000 /usr/bin/kgcc
chmod 000 /usr/bin/cc
chmod 000 /usr/bin/gcc
chmod 000 /usr/bin/*c++
chmod 000 /usr/bin/*g++
```

#### Comments

I don't think it adds much to security these days. Lot of people remove compilers because they can theoretically be used for exploits.

In my opinion, if the attacker can get in to your server, he can get own tools in as well so removing things like compilers, and emacs, and junk like that doesn't add much to your overall security.

If you care about more control, you can create an additional group and add users to it:

```bash
groupadd compilers

for i in byacc yacc bcc kgcc cc gcc "*c++" "*g++" ; do

  chown :compilers "/usr/bin/${i}"
  chmod 0750 "/usr/bin/${i}"

done
```

On the other hand common wisdom of "proper way to do things" is to put the minimum necessary on servers.

If you don't need the compilers, don't put them on there. It's one more thing that, if cracked, a system cracker could potentially use against your system, and it introduces more binaries that could have bugs in them that can be taken advantage of.

#### Useful resources

- []()
