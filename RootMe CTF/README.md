# RootMe: Complete CFT Writeup for Try Hack Me Beginners

Lately, I've been feeling ready to do more CTF's on Try Hack Me. Now previously, when I did the Pickle Rick CTF, I felt a little lost - or a bit _in over my head_. But once I put my mind to it and kept on persisting, it turned out to be pretty easy, and I promise you that the RootMe CTF is no different! 😊 

Today we will beat the [RootMe CTF](https://tryhackme.com/room/rrootme) on Try Hack Me. So grab a comfy neck pillow, open up your terminal, and let's get hacking.

![RootMe](https://media.giphy.com/media/12XDYvMJNcmLgQ/giphy.gif)

---

## Task 1: Deploy the Machine 
Connect to [TryHackMe network](https://tryhackme.com/room/rrootme) and deploy the machine. If you don't know how to do this, complete the [OpenVPN](https://tryhackme.com/room/openvpn) room first. 😁

---

## Task 2: Reconnaissance 
**<u>Scan the machine, how many ports are open? </u>**
Open up your terminal and run the command `nmap -sV 10.10.85.26` to find all the information needed for this task. 
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/23ivqiaoz9vuniz61pqx.png)
 
Ports 22 and 80 are open , thus `2` ports are open.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5jk146m81mi1sztsm3qq.png)
 
**<u>What version of Apache is running?</u>**
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/23ivqiaoz9vuniz61pqx.png)

We can see that Apache httpd `2.4.29` is running.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1jw571ro6tatzdlbclhh.png)

**<u>What service is running on port 22?</u>**
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/23ivqiaoz9vuniz61pqx.png)

We can see that `ssh` is running on port 22/tcp.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0ghs1xor11tmdmt0aael.png)

**<u>Find directories on the web server using the GoBuster tool.</u>**
Open up gobuster and run the command `gobuster dir -u 10.10.85.26 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30` and all will be revealed. I use the dirbuster wordlist because it is shorter, but you can use the usual rockyou.txt if you want.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/571nvefnvul67drcq3k9.png)
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/itrgcmewopul5w21rb70.png)
 
**<u>What is the hidden directory?</u>**
`/Uploads` is not for users to see since it shows our  file directories, but /panel/ can be used by users to upload files. Thus `/panel/` is the hidden directory.

![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l5ynzqnf2foz0q4nj1hf.png)
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/501j1ua1nxkgumkm0doi.png)
 
---

## Task 3: Getting a shell 
**<u>Find a form to upload and get a reverse shell, and find the flag.</u>**

**Step One: Setting up our reverse shell.**
Let's first open up our IP Address in our browser. We are met with a simple, static page without any navigation, links, buttons etc. 
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1cbr14bit198bqzczjjl.png)

From our gobuster results we know that we have two directories that are of importance, our `/panel`:
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9jakp0m8j22k6u3i4ht5.png)
 
And our `/uploads`:
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qh1hdhg6y7yked1pnkqg.png)
 
On your Desktop, make a new empty php file. Call it anything, like hello.php - it does not need to contain any code. Try to upload it. You will see that we get an error since the .php extension is blocked due to site filtering and validation. I recommend you have a look at [WebSecAcademy's tutorial](https://portswigger.net/web-security/file-upload) on this. We will need to upload our php file in a different format, like php.png, or [phtml](https://www.file-extension.org/extensions/phtml).
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ttaf8bstwdo7h2jomw2o.png)

Our php file that we will upload will contain our reverse shell script. You can download a common php [reverse shell](https://www.imperva.com/learn/application-security/reverse-shell/) file from [here](https://pentestmonkey.net/tools/web-shells/php-reverse-shell). Once downloaded, copy the `php-reverse-shell.php` file to your Desktop.

Before we continue, I want you to open up your terminal again and run the `ifconfig` command. Make note of the IP Address of your attacker machine, because we will need it. 👀 
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3cyd3cci74sermqkr8x0.png)

Open up your php-reverse-shell.php file that you saved on your Desktop and scroll down to the section listing your `$ip and $port`. You can enter any port you want (that's not taken), or leave it as 1234. Replace the IP address with the IP address you copied from your ifconfig result.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jew5hw4z2zqft51fi905.png)

Go back into your terminal and start up a netcat scan (by running `netcat -nlvp 1234`) -on the port number you provided (in this case, 1234). When we've successfully run our reverse shell we will be able to see it here.  
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fta6gz7r60id6wb11cyr.png)
  
Now, open up your terminal and cd into Desktop, where we saved our php-reverse-shell.php file. We need to turn this file into an executable by running the `chmod +x php-reverse-shell.php` command.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ab4m7djp62b5r8nf5o8k.png)

Now, to take care of that pesky .php extension. Let's convert it into a .phtml file by running mv php-reverse-shell.php php-reverse-shell.phtml. If you look for the file on your Desktop, you will see that it is now called php-reverse-shell.phtml.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ctvsotyddhkcfioy946a.png)
 
Okay, now we can go and upload this file on our /panel directory.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1zgpkvmozt1fpcdnenm2.png)
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kmip9t0zjb7a19pk46oo.png)

Success! Now when we head over to our /uploads/ directory we can see that our file is there. Click on it and head over to the terminal where your netcat scan is running.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qieve6co0j5wp5v8yt1t.png)
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5p2n0w9chkhddvr0ac62.png)

We have successfully set up and executed our reverse shel! Now we need to go ahead and access our user.txt file.
  
**Step Two: Reading our user.txt file.**  
In your terminal, under your netcat scan, let's run the `ls` command to see what we can find in our current directory, ![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8gstrtui84a7rach8i6l.png)

Now you can go ahead and cd into each and every directory to try and find that user.txt file, but you'll be wasting your own time! We can find it by simply running the command: `find / -type f -name user.txt`.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/q1y2x4k6lnvtv22bvcja.png)
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ssee794vxchw7sgf5938.png)

We now know that the user.txt file is in the var/www/user.txt directory. We can now cat into it and access it's contents!
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/q39chwo1bl7b37ywk215.png)
 
Tada! We've found our flag! 😊
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/adqaed9usb4p8y8815gp.png)
 
---

## Task 4: Privilege escalation 

**<u>Search for files with SUID permission, which file is weird? </u>**
To search for files with SUID permission, we can run the `find / -type f -user root -perm 4000 2>/dev/null` command. We can see that the weird file is `/usr/bin/python` - read more on this at [GTFObins](https://gtfobins.github.io/gtfobins/python/#capabilities).
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ph91dqmhz5zny0mdrzz2.png)
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zg1mwogsrg83nga29w7v.png)

**<u>Find a form to escalate your privileges.</u>**
If you have a look at the GTFObins link provided above, you can see that we can exploit this SUID via the `python -c 'import os; os.execl("/bin/sh", "sh", "-p")'` command. 
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/msjgetdjx802wjg1q8c1.png)

When we run the `id` and `whoami` commands, we will see that we now have root access. 
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/18ieh3arrnhkn7ra5ros.png)

We've successfully escalated our privilege from normal user to root.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/u15cyocxnynxszmzhprw.png)

**<u>root.txt.</u>**
To read what's in the root.txt file, we first need to cd into root to see what is under our /root directory.
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8to3w1meekpf8ptrwx6q.png)
 
From here on we are clear. Run `cat root.txt`, and you are done! 👍
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8prtz81rkp9jbykegvo8.png)
![RootMe](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hiboftl02s2tay3qq5hj.png)

---

## Conclusion
And that's our RootMe CTF done! Good job. I hope this was simple enough to follow with, and let me if you know of other methods/tools that you used to complete this CTF. See you next time! 😆

  
 
