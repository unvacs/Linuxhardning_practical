You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Permissions and Limits](#permissions-and-limits)**
  * [Package signatures](#package-signatures)

## Permissions and Limits

### Package signatures

#### Rationale

#### Solution

###### /dev/shm

```bash
# C2S/CIS: CCE-80153-0 (unknown), CCE-80154-8 (unknown), CCE-80152-2 (unknown)

tmpfs  /dev/shm  tmpfs  rw,nodev,nosuid,noexec 0 0
```

#### Policies

<code>C2S/CIS: <a href="">CCE-80153-0 (unknown)</a></code>

#### Comments

#### Useful resources

- []()