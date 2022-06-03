# The Complete Write-up for a Simple CTF (Try Hack Me)

Want to be cool? Want to be a hacker? Want to be old school? Well look no further kid, because today we are going to hackify this [Simple CTF on Try Hack Me](https://tryhackme.com/room/easyctf) (enter electric guitar and flying unicorns here). 😎

When you're ready, start up that machine, crack your knuckles and let's break this CTF apart.

![Simple CTF](https://media.giphy.com/media/iAKXyzgLVtKsU/giphy.gif)

---

## Task 1 Simple CTF 
<u>**How many services are running under port 1000?**</u>
By now you will know that to find the services that are running on a port we need to run a nmap scan. Run the following command to scan for all services under port 1000: `nmap -p1-1000 <your machine IP>`.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pgapz6vghmslg9arsv1s.png)
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ve438qw8dcbqc6h5qn15.png)

We can see that there are two services running: ftp and http.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vyhid7gd4yuhg6or585v.png)

<u>**What is running on the higher port?**</u>
Now we need to scan for services above 1000. We can do this by simply running a normal nmap scan via `nmap -sV <your machine IP>`.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/w26jmhkpqsl7xxuvl8cu.png)

We can see one port above 1000, which is port 2222/tcp and it is running ssh.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/mueds1cwlohsipcrxxbe.png)

**<u>What's the CVE you're using against the application?</u>**
Before we can look for the CVE of our web application, we need to look at what our application contains. Let's start at our home page. Open up your IP address in your browser. We are greeted with a Default Ubuntu Page.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6imt4onp92idevv0npxr.png)

Okay, there's not much for us there, so let's see what other directories we can find to see which services we can exploit. Let's run a gobuster scan to find these hidden directories. Open up gobuster and run the following: `gobuster dir -w /usr/share/dirb/wordlists/common.txt -u http://<your machine IP> -t 30 -o output.txt`.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/adkjrbth2ffek8ri1398.png)

We can see that we have the following hidden directories that could be of importance to us: `/robots.txt` and `/simple`. Go back to your browser and open /robots.txt. It's useless. 😑
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wy66e7tkdqngi9b6acc9.png)

Okay, let's try /simple. Hoorah! We are met with a page to CMS Made Simple. When we scroll to the bottom, we can see the version of CMS Made Simple used for this site. We can use this to find exploits.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hvj6cy0kgsrqexsbzumd.png)
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lrcxfhl0h6m0thflnngl.png)
  
When we search for it in [Exploit-DB](https://www.exploit-db.com/exploits/46635) (you can click here to go to the exploit), we can see the CVE for this application.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m66o5m30nydvl4wcnt3e.png)
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/r2dqi1o2qgllx5izt6sz.png)
 
<u>**To what kind of vulnerability is the application vulnerable?**</u> 
From the page above, we can see that it is a [sqli](https://portswigger.net/web-security/sql-injection) vulnerability.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1s5aef9sal2xtbb8zxll.png)
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cnjrxu1p96qdrga5cl6v.png)

<u>**What's the password?**</u> 
Okay, now we need to use the CVE found above to exploit our application to find a password. If you are using python3, do not install the exploit from Exploit-DB (since you will have to pip install things to run python). Instead, head over to this [GitHub page](https://github.com/4nner/CVE-2019-9053) and download the `exploit.py` file for this CVE that is converted into python3. You can copy this file onto your desktop (I saved mine as 46635.py).
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/j66mz56in5erby4u4k4l.png)
 
Now, open up your terminal and cd to your Desktop. We need to run the `chmod +x exploit.py` command to convert this script into an executable.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lnn2lqj9g2n0vbiwst6r.png)

Okay, then we can simply run the bash script to run the exploit against our target IP: `python3 exploit.py -u http://<your machine IP>/simple --crack -w /usr/share/wordlists/rockyou.txt`
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/84lotoiwuk8h8ne3y3ef.png)

This will take some time, but you will eventually find the password: `secret`.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uuy2crrlxaix8ezfhzcp.png)
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/f73u84e35bj9hhwxgqk7.png)
 
<u>**Where can you login with the details obtained?**</u> 
We can log in to ssh with these details, since we have the username `mitch`, the IP address, and his password.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/k7q7eo7466qxzculakpn.png)
  
<u>**What's the user flag?**</u> 
Let's log into ssh using Mitch's credentials. `Run ssh mitch@<your machine IP> -p 2222` - 2222 is ssh's port.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cxkfc48orhzio2berp0r.png)
 
Let's list all the directories using ls -a. We can see a file named user.txt, and we can read the contents of this file via `cat user.txt`.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/q8ywyeb5psuczlr1emsd.png)
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ltsmhf7ovv9epi5ar0ku.png)

<u>**Is there any other user in the home directory? What's its name?**</u> 
Let's first cd into our /home directory. We can see that there is one other user besides Mitch, Sunbath.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ar1e9vb9qndryd4e6alk.png)
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/spnlssiox0ogv229ilwz.png)

<u>**What can you leverage to spawn a privileged shell?**</u> 
We can use [vim](https://gtfobins.github.io/gtfobins/vim/) to spawn a privileged shell.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5kzh80fbf915uvo5lc8v.png)
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4ar6o6lgq6l2nq1s2ykm.png)

<u>**What's the root flag?**</u> 
Let's use vim to spawn a privileged shell. You can read more on this in the in  link provided above, but simply put, we can do it via the following command: `sudo vim -c ':!/bin/sh'`. When we run the `id` command, we see that we now have root access.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6luwxt6kplst7alhcdnw.png)

Now, let's cd into /root. We can see there is a file named `root.txt`.
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/25mrr5pltpk29o3h47zs.png)

We have root access, so we can simply `cat root.txt` to get the final flag. 
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zk2hummm8efi5l0394z2.png)
![Simple CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/stjafvx1ryjqoclqbgdt.png)
 
---

## Conclusion
I hope this was easy enough to follow, and if it was, congratulations on completing the Simple CTF! You are now a master hacker, and there is nothing more to teach you. Keep an eye on your inbox for an email from the CIA. They'll be in touch soon! 👾

Until next time, happy hacking!
 
 


