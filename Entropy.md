You can [file an issue](https://github.com/trimstray/the-practical-linux-hardening-guide/issues) about it and ask that it be added.

---

## Table of Contents

- **[Entropy](#entropy)**
  * [Alternative entropy sources](#alternative-entropy-sources)

## Entropy

  > Not available from C2S/CIS standard.

Entropy is the measure of the random numbers available from `/dev/urandom`.

It is important for a secure operating system to have sufficient quantities of entropy available for various crypotographic and non-cryptographic purposes, such as:

- generation of cryptographic keys
- TCP port randomisation (NAT, outbound connection)
- TCP sequence number selection
- writing random files for testing network functionality and throughput
- overwriting hard disks prior to reuse or resale or encryption

### Alternative entropy sources

#### Rationale

It is generally recommended wherever entropy is used heavily to supply additional entropy sources.

#### Solution

###### Haveged

Haveged was created to remedy low-entropy conditions in the Linux random device that can occur under some workloads, especially on headless servers.

```bash
# Add haveged daemon to autostart
systemctl enable haveged

# For temporary change:
echo "1024" > /proc/sys/kernel/random/write_wakeup_threshold

# For permanent change (edit /etc/rc.local):
/usr/local/sbin/haveged -w 1024
```

#### Comments

To check the status of your server’s entropy, just run the following:

```bash
cat /proc/sys/kernel/random/entropy_avail
```

To check the maximum limit of entropy:

```bash
cat /proc/sys/kernel/random/poolsize
```

#### Useful resources

- [Haveged](http://www.issihosts.com/haveged/)
