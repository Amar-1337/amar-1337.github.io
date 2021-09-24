---
layout: default
---

<center> 
    <big>Writer - HTB</big>
</center>

<center>
    <img src="https://pbs.twimg.com/media/E7Yb_WKXoAk4o3M?format=jpg&name=4096x4096">
</center>

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


*   Information
*   name: <p1> Writer </p1>
*   OS: <p1> Linux </p1>
*   Difficulty: <p1 style="color:red;">Medium</p1>
*   Points: <p1> 30 </p1>
*   Release: <p1 > 2ND AUGUST, 2021 </p1>
*   IP: 10.10.11.101
*  https://app.hackthebox.eu/machines/Writer

## Summary
- Reading files and logging into the system via SQL injection.
- Read source code to find command injection vulnerability and get web shell.
- Read web path to discover configuration files. Read Mysql account secret login to get hash.
- Use hashcat to crack the hash to get the password and get the user.txt.
- Mapping port 25 out, execute a command via /etc/postfix/disclaimer to further elevate privileges and read john's id_rsa.
- Raise privileges via apt-get.


# <big>nmap</big> 

```js 
root@kali:~# nmap -sC -sV 10.10.11.101
Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-24 12:22 CEST
Nmap scan report for writer.htb (10.10.11.101)
Host is up (0.61s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 98:20:b9:d0:52:1f:4e:10:3a:4a:93:7e:50:bc:b8:7d (RSA)
|   256 10:04:79:7a:29:74:db:28:f9:ff:af:68:df:f1:3f:34 (ECDSA)
|_  256 77:c4:86:9a:9f:33:4f:da:71:20:2c:e1:51:10:7e:8d (ED25519)
80/tcp  open  http        Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Story Bank | Writer.HTB
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: 14m02s
|_nbstat: NetBIOS name: WRITER, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-24T10:37:18
|_  start_date: N/A
```


<big> Pages redirecting to writer.thb so lets add to /etc/host<big>

```js
10.10.11.101    writer.htb
```

<img src="https://rustlang.rs/images/htb-writer/001.png">

<big> Nothing special on website so im gonna try to find something like login page </big>

```js
wfuzz -w /usr/share/dirb/wordlists/big.txt -u http://writer.htb/FUZZ --hc 404 -t 200
```
<img src="https://gcdn.pbrd.co/images/7zOTtLDkbG2N.png?o=1">

<big> Lets check administrative page </big>

<img src="https://rustlang.rs/images/htb-writer/003.png">

<big> Redirect us to admin login page, im gonna try something like admin:admin or admin:password. If doesn't work im gonna check for sql injection bypass </big> <br><big>So after 5-10 minutes i got in with **admin ' or '1'='1**
<img src="https://rustlang.rs/images/htb-writer/005.png">

<big> I check website and didnt find anything useful, checked burp and got something useful. </big> <br> On website you can post **story** and we can upload picture where we can do reverse shell through **burp**.<br>Post request looks like this.</br>

```js
POST /administrative HTTP/1.1
Host: writer.htb
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 18
Origin: http://writer.htb
Connection: close
Referer: http://writer.htb/administrative
Upgrade-Insecure-Requests: 1
uname=demo&password=demo
```


[Back to main page](./index.html)
