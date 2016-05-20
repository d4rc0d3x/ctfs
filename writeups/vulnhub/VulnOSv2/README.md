###### Description: Writeup of VulnOSv2 VM in Vulnhub by the author c4b3rw0lf

Let's begin by scanning the host: 

```
# nmap -A -T4 -sV -PN -p- 192.168.0.15

Nmap scan report for 192.168.0.15
Host is up (0.00022s latency).
Not shown: 65532 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     (protocol 2.0)
| ssh-hostkey: 1024 f5:4d:c8:e7:8b:c1:b2:11:95:24:fd:0e:4c:3c:3b:3b (DSA)
| 2048 ff:19:33:7a:c1:ee:b5:d0:dc:66:51:da:f0:6e:fc:48 (RSA)
|_256 ae:d7:6f:cc:ed:4a:82:8b:e8:66:a5:11:7a:11:5f:86 (ECDSA)
80/tcp   open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-title: VulnOSv2
6667/tcp open  irc     ngircd
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at http://www.insecure.org/cgi-bin/servicefp-submit.cgi :
SF-Port22-TCP:V=6.40%I=7%D=5/20%Time=573F2008%P=x86_64-pc-linux-gnu%r(NULL
SF:,2B,"SSH-2\.0-OpenSSH_6\.6\.1p1\x20Ubuntu-2ubuntu2\.6\r\n");
MAC Address: 08:00:27:57:4F:AA (Cadmus Computer Systems)
No exact OS matches for host (If you know what OS is running on it, see http://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=6.40%E=4%D=5/20%OT=22%CT=1%CU=39556%PV=Y%DS=1%DC=D%G=Y%M=080027%T
OS:M=573F2056%P=x86_64-pc-linux-gnu)SEQ(SP=106%GCD=1%ISR=10B%TI=Z%CI=I%TS=8
OS:)SEQ(SP=106%GCD=1%ISR=10B%TI=Z%CI=I%II=I%TS=8)OPS(O1=M5B4ST11NW7%O2=M5B4
OS:ST11NW7%O3=M5B4NNT11NW7%O4=M5B4ST11NW7%O5=M5B4ST11NW7%O6=M5B4ST11)WIN(W1
OS:=7120%W2=7120%W3=7120%W4=7120%W5=7120%W6=7120)ECN(R=Y%DF=Y%T=40%W=7210%O
OS:=M5B4NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N
OS:)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=
OS:S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF
OS:=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=
OS:G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 1 hop
Service Info: Host: irc.example.net

TRACEROUTE
HOP RTT     ADDRESS
1   0.22 ms 192.168.0.15

OS and Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1041.99 seconds
```

So we discovered it has the following services running:
- SSH (22/TCP)
- HTTP (80/TCP)
- IRCd (6667/TCP)

Firing up the browser we encounter a black screen pointing us to http://192.168.0.15/jabc/. 

Following this link we reach another page called "JBC Just Another Bioware Company". Not many interesting stuff here so I decided to have a look at the website source code. 

Poking around some pages I've found /jabc/modules/system/ with apache index enabled, with many many files:

```
Index of /jabc/modules/system
[ICO]	Name	Last modified	Size	Description
[PARENTDIR]	Parent Directory	 	- 	 
[ ]	html.tpl.php	2016-04-16 15:17 	2.7K	 
[ ]	image.gd.inc	2016-04-16 15:17 	12K	 
[ ]	language.api.php	2016-04-16 15:17 	6.4K	 
[ ]	maintenance-page.tpl.php	2016-04-16 15:17 	2.9K	 
[ ]	page.tpl.php	2016-04-16 15:17 	6.8K	 
[ ]	region.tpl.php	2016-04-16 15:17 	1.3K	 
[TXT]	system.admin-rtl.css	2016-04-16 15:17 	1.4K	 
[TXT]	system.admin.css	2016-04-16 15:17 	5.0K	 
[ ]	system.admin.inc	2016-04-16 15:17 	113K	 
[ ]	system.api.php	2016-04-16 15:17 	193K	 
[ ]	system.archiver.inc	2016-04-16 15:17 	3.0K	 
[TXT]	system.base-rtl.css	2016-04-16 15:17 	869 	 
[TXT]	system.base.css	2016-04-16 15:17 	5.2K	 
[ ]	system.cron.js	2016-04-16 15:17 	489 	 
[ ]	system.info	2016-04-16 15:17 	462 	 
[ ]	system.install	2016-04-16 15:17 	112K	 
[ ]	system.js	2016-04-16 15:17 	4.6K	 
[ ]	system.mail.inc	2016-04-16 15:17 	4.5K	 
[TXT]	system.maintenance.css	2016-04-16 15:17 	811 	 
[TXT]	system.menus-rtl.css	2016-04-16 15:17 	551 	 
[TXT]	system.menus.css	2016-04-16 15:17 	2.0K	 
[TXT]	system.messages-rtl.css	2016-04-16 15:17 	176 	 
[TXT]	system.messages.css	2016-04-16 15:17 	961 	 
[ ]	system.module	2016-04-16 15:17 	137K	 
[ ]	system.queue.inc	2016-04-16 15:17 	12K	 
[ ]	system.tar.inc	2016-04-16 15:17 	62K	 
[ ]	system.test	2016-04-16 15:17 	108K	 
[TXT]	system.theme-rtl.css	2016-04-16 15:17 	811 	 
[TXT]	system.theme.css	2016-04-16 15:17 	3.6K	 
[ ]	system.tokens.inc	2016-04-16 15:17 	7.9K	 
[ ]	system.updater.inc	2016-04-16 15:17 	4.6K	 
[ ]	theme.api.php	2016-04-16 15:17 	8.8K

```
All TXT files are basically CSS files, not too important information there, so I continued browsing through JABS products and found the page: /jabc/?q=node/7.

Inspecting the source I found the following lines: 
```
<p><span style="color:#000000">For security reasons, this section is hidden.</span></p>
<p><span style="color:#000000">For a detailed view and documentation of our products, please visit our documentation platform at /jabcd0cs/ on the server. Just login with guest/guest</span></p>
<p><span style="color:#000000">Thank you.</span></p>
```

Now we've got a new link called /jbacd0cs/ and also some guest/guest credentials. Accessing the URL in question it gets us to a page called "opendocman".

Using the guest/guest credentials it was possible to log into the application.

We are presented with something like a database administration but with guest privileges.
