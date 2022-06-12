# Daily Bugle: A Spiderman Themed Try Hack Me CTF 🕸️

If you had to choose between Marvel and DC, who would you choose? I think we all have a favorite, but we cannot deny the fact that we all like Spiderman. This is not a fact, but an opinion so don't hold me to this bold statement! 😅

Today we are going to hack the Daily Bugle, are you ready? 🕵️‍♀️

![Daily Bugle CTF](https://i.imgflip.com/5imzuv.jpg)

---

## Access the web server, who robbed the bank?
Once your machine is loaded, let's open up the IP address in our browser. We won't have to scroll far before we see who robbed the bank: Spiderman!
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6pb7dwh82a151vzi9uqu.png)
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xd2vou8t1yoa9mvf94bt.png)
  
---

## What is the Joomla version?
Okay, now that we've launched our machine and had a look around our main page, we can start enumerating. Open up your terminal and start up a nmap scan to see which services are running. 
`nmap -sV -Pn <your machine IP>`
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cuf0225azgokye5blol3.png)

We can see that ssh, http and mysql are running. Let's continue with running a gobuster scan to see which hidden directories we can find. 
`gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -u http://<your machine IP> -t 50`
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7wkocrdmv8yytodga2k5.png)
 
When we head over to `<ip>/administrator`, we see that it opens up a Joomla! login page. Okay, now we need to find the version that is running. We can do this with joomscan.
`joomscan -u http://<your machine IP>/administrator/`
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/v0bst8wehtw1e9ni55tz.png)
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/s8a90x1v64wvanq202eo.png)

Tada! We've found our Joomla version.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rh8yw6ch4jq7nabo1qx0.png)
 
---

## What is Jonah's cracked password?
Okay so we can do this via finding a python exploit, or by running SQLMap (which takes forever). I'll show you the python script way since the lab instructions encourage us to try it.

Let's see if there is a valid exploit with searchsploit.
`searchsploit joomla 3.7.0`
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8u2xj9khnt9rugqhqiow.png)

We can see that there is a valid SQL Injection exploit available for us to use. Now you can go over to Exploit-DB and download this exploit if you are doing the SMLmap method, but we won't be able to use this. Instead we need a python conversion of this exploit. Luckily, I got you! 

Download the python exploit from [here](https://github.com/SiopySec/CVE-2017-8917).
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rsw4cxa7j49p4ibdvaqv.png)
  
I renamed this file as `exploit.py` and saved it in my `/Downloads` directory. Now, go back into your terminal and cd into the directory of the downloaded exploit. If you read the instructions from the GitHub page above, you will see that we need to install two packages, so let's do that. While we're at it, let's also turn our `exploit.py` into an executable.

1. `pip install art`
2. `pip install beautifulsoup4`
3. `chmod +x exploit.py`

![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/iawik6h7a9g6lndztjhb.png)
 
Once you've done all of the above, we can run our exploit.
`python3 exploit.py`
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/czdw941x5dekizfjefky.png)

After a bit, you will se the user's password. Let's copy it and create a new file called `pass.txt` (I just did it in my /Downloads directory). Paste this value into this new file.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rexliw3e239ml5q9sn56.png)
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/63ya8kjkxyxpv6gsnnvj.png)
 
Now, to crack this password. We can use John The Ripper for this. Run the following command in your terminal: 
`john pass.txt --wordlist="/usr/share/wordlists/rockyou.txt"`
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cxe34emi7t5lxadhhvwm.png)

Thus we now know that Jonah's password is spiderman123.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/s1nifa7yz5fjc0fv5zla.png)
 
---

## What is the user flag?
Okay, now that we have our username and password (Jonah:spiderman123), we can attempt to log into our Joomla site.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kep6y9ffti61ncqvybni.png)

Once in, you will be met with a control panel for Jonah. Since our site is built with PHP, we can create a reverse shell to gain access to the ssh service found above.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qn18a97hxxtbf3lpnb4l.png)

You can download the reverse shell from [Pentestmonkey's](https://pentestmonkey.net/) website or via the following command:
`wget https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php`
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xrgxjin12hl3c08b8z3j.png)

Let's get that netcat listener out of the way. We can keep our port as 1234, it doesn't really matter unless you feel finicky about it.
`sudo nc -nlvp 1234`
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fexl370pynneieet5fjx.png)

Head over to your Joomla dashboard and navigate to the `Template Customizer`. Now, grab that reverse shell file that you just downloaded (it will be in your Downloads directory, and you'll have to extract it), and paste it into your /index.php (or error.php) file. Remember to change the IP address of your reverse shell to the IP of your attacking machine (OPENVPN) and save.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l7yaqe951kyilkws7hsn.png)

Click on `"Preview Template"` and check on your netcat listener, we now have a shell! If the preview template shell doesn't work, just navigate back to index.php in your browser and that should work.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/e9fadv2a9h5dkyb6miep.png)
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yw9qsy7iywhs5ualmgmz.png)
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0w0cjws3afhg555ichhp.png)

Let's run the `cat /etc/passwd` command so that we can view the list of the system's accounts. We can identify a user named `jjameson` (we will use this for our ssh login later).
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hue5lpp7ad2alaj6fmrg.png)

Next, let's cd into our `/var/www/html` directory, which is the base directory for our site and it will list all the root files. One file of interest is the `configuration.php` file (you can read each file as this is all trial and error to find a file that has valuable information).
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jkookrk1214sbpbbf2lc.png)

Reading the contents of this configuration.php file, we see a password for our **jjameson** user.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qenq76kz79f8jkgu2uot.png)

Let's log into ssh using these credentials (jjameson: nv5uz9r3ZEDzVjNu). We can see our user.txt file listed immediately.
`ssh jjameson@<your machine IP>`
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4kg8efzqqhyzbflhijqx.png)

Read the contents of `user.txt`, and voila, you have your flag!
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2suf1exrzi2b69v06g75.png)
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qt9b61dzyzerovmrhl4h.png)
   
---

## What is the root flag?
Okay, so we are in the final stretch of completing this CTF. Let's see what sudo privileges we have via the `sudo -l` command. I apologize for the poor screenshot I took. What is important is that we can run the `yum` command.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qnbr85lqx10uhvvm0ef7.png)
 
Let's head over to [GTFObins](https://gtfobins.github.io/gtfobins/yum/) and see how we can use yum. If the yum binary is allowed to run as superuser by sudo, it does not drop the elevated privileges and may be used to access the file system, escalate or maintain privileged access.
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bqiq80ll1qwhvrz6ibn0.png)
 
Follow this section of our yum page to get to root:
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d4xrxolj4dxddtoh9gxe.png)
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vkkpr5fqh93bo11eqlr2.png)

Now we can view our root.txt file that is found in `/cat/root.txt`. We have our flag!   
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xhqo0rveequmia1vlqdx.png)
![Daily Bugle CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vlz7pbh929xbs7889yz2.png)

---

## Conclusion
You just hacked the Daily Bugle, congratulations! I hope that this was easy enough for you to follow, and until next time, happy hacking! 😁
