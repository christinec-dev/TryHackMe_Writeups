# How To Not Get Skiddie Schooled by the Pickle Rick CTF on Try Hack Me
Today I was met with my first hacking challenge - only six days in to my learning journey. I won't lie to you, it almost made me cry. I was _**skiddie schooled**_ by a CTF (Capture The Flag) challenge that is labelled as easy. But, with a little bit of persistence, it turned out to be easy breezy chicken sneezy! 😁 

Today we will beat the Pickle Rick CTF on [Try Hack Me](https://tryhackme.com/dashboard) together. So grab a comfy neck pillow, open up your terminal, and let's get hacking.  

## Introduction: Rick's Missing Ingredients 
If you're not on the Pickle Rick CTF page already, head on over to it via [this](https://tryhackme.com/room/picklerick) link and get your machine up and running. I'm running it locally via [OPENVPN](https://tryhackme.com/room/openvpn), but you can do it with Try Hack Me's Attackbox.

When we load up the page, we are met with the following introduction:

![CTF INTRO](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tcagid9qjq6h29rnbswa.png)

---

## Step One: Finding The First Flag
Silly Rick and Morty, it seems that they have forgotten the ingredients to turn a pickle back into a human. Haven't we all? No worries, that is why we, the friendly neighborhood hackers are here to help. Let's open up our **https://LAB_WEB_URL.p.thmlabs.com** on our machine and see what's up.

![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lfyo07e2l79tp5ht6vaz.png)
 
We are met with a bare website that has no navigation, input fields, buttons - nothing. So how do we find the flags if there's nothing on the site? Well, let's start by looking at the page source to see if we can find any information. 🔍

![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/k8yl1y10indid9kzmibh.png)
 
We can see that we have a username for something - maybe a login form. Copy this somewhere because we will use it later. Let's see if we can find which page this is needed for by opening our terminal via CTRL + ALT + T. 

Let's run a [Nmap](https://www.redhat.com/sysadmin/quick-nmap-inventory) scan on our IP Address. If you are not familiar with Nmap, no worries,, just know that it is a very powerful tool that allows us to scan addresses on local or remote networks to see which operating systems, machines, protocols, or ports it's running. 

```
nmap 0.0.0.0 (enter your machine's IP address)
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6gw8bryi1e7e2v3wl0p5.png)

We can see that it runs three different ports. We will be focusing on the open two of them which can be further elaborated as follows:

- `22/tcp` is usually occupied for Secure Shell (SSH), secure logins, file transfers and port forwarding. 
- `80/tcp` is usually used for our Hypertext Transfer Protocol (HTTP).

Now that we know which ports are open, we can go ahead and run a [Nikto](https://www.freecodecamp.org/news/an-introduction-to-web-server-scanning-with-nikto/) scan on our IP address. Nikto helps us find potential security threats in websites and web applications via scans which could return information such as service misconfigurations, insecure files/programs, and more.

```
nikto -h http://10.10.78.35
```

![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/swcgyshdcu3pvup0sj7h.png)

When the Nikto scan has finally completed, and your birthday has already passed (because it takes forever 😑), we can denote two important pieces of information - confirming our earlier suspicions. We can see that we have two files that we can now use to find information: **robots.txt** and **login.php**. If you want, you can also run a [Dirb](https://www.kali.org/tools/dirb/) scan, but it doesn't give us any more information than our Nikto scan.

![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4lh8sscwlr2rg6huqwnh.png)

Let's first open up our [robots.txt](https://developers.google.com/search/docs/advanced/robots/intro) to see what we can find. Perhaps **Wubbalubbadubdub** is a password? Keep this value close.  

![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/e2ze57zl0ggy75aj2i5c.png)

Next, lets head on over to **login.php** and see if we can enter our username from earlier with our Wubbalubbadubdub value from the robots.txt file. Voila, we're in!

![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kkarc4acgxd0aiburgs1.png)

---

![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/n9bwqasb8g85n0fjpeck.png)
 
We are met with a command panel that we can use for command tools or possible [injections](https://crashtest-security.com/command-injection/). Let's see our current path of the working directory to see where we are at. We can use the [pwd](https://www.ibm.com/docs/en/aix/7.1?topic=p-pwd-command) command for that.

```
pwd
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5lnee790usq7tamvm0xv.png)

We can see that we are in the **/var/www/html** directory, so to cd to the contents of our system we will need to say **cd ../../../** - but before we cd into anything, lets see if there is anything in the current directory. 

```
ls -a
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/n9t41gzn6qidxmeiwnw1.png)

The file **Sup3rS3cretPickl3Ingred.txt** seems suspicious, why don't we go ahead and open it up? 🧐
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/p55rlvgea5xzlvrx7iuf.png)
 
Woohoo, and so we have found our first flag! 😁
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/og6v0ge4gdj9wx1oa2rl.png)

---

If you were wondering what's in the **clue.txt** file, here you go:
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sfi53a1o9fmunibrk13r.png)

There is also an **/assets** directory, but there is nothing of importance for us here.
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rogl2x0nznh2n80pctmb.png)

---

## Step Two: Finding The Second Flag 
With our first flag under the belt, let's move on. Go back to the command panel and cd into ../../../ so that we can list everything that is under our current working directory. 

```
cd ../../../; ls-a;
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/25ht99t1thx3l91ifrwp.png)

We can see that from here we can navigate into the **home folder**. Let's do so and list all the content that is under that directory to see what important data we can find.

```
cd ../../../; ls-a; cd '/home'; ls -a
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nrzsurkwkk8kx9jza2ui.png)

Since this is a Rick and Morty CTF, let's see what is underneath the **/rick** directory.
```
cd ../../../; ls-a; cd '/home/rick'; ls -a
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0jha159e0oc2pa67zhaf.png)

Mmh, I bet the **second ingredients** directory contains something valuable. Since it is not a filetype such as .txt or .php, we cannot pop it in to our URL. We will need to see if we can "read" it in the command panel via commands such as [cat](https://www.tecmint.com/13-basic-cat-command-examples-in-linux/). 
 
There is a problem though! If we try to read the contents with cat we get pranked. It seems that our program is more of a _dog_ person, as the command is disabled to make it harder for us to see the contents of the file. We need to find an alternative command! 😼

![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/b4y3ifsh2ay6q5s562qv.png)

Maybe our command panel should listen to Angela from The Office! 😆
![CTF Page](https://media.giphy.com/media/BDc39OeNgqCXiig9TV/giphy.gif)

Not to worry noble knight, let's try the cat's less liked brother - get it, _less_? `*Sigh*`, a joke is never funny if you have to explain it. 😪

```
cd ../../../; ls -a; less '/home/rick/second ingredients';
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ql0n8o33wqnuuc65snz0.png)

We found the flag! Now, let's get that last one! 
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qihph3v10aze5m9xmrvs.png)
 
---

## Step 3: Finding The Third Flag 
I'm not going to lie to you, I _**struggled**_ finding this one. But that's only because I didn't understand that we needed to get into the root folder because we've robbed our home folder of all it's valuables, there's nothing left for us here. 

Let's see what privileges we need to get into the **root folder**. To do this we need to make use of the **sudo -l** command, which will list the allowed (and forbidden) commands for the invoking user access on the current host.
 
```
sudo -l
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hi3ihbhh78f993z3fu47.png)
We can see that we do not need any password to get into the root folder. This means that we can just list all it's contents directly. 😎

```
sudo ls /root
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9xt2w38fvhdpb3gsaxgy.png)

Good job getting to this point. Since we can assume that our third flag will be in **3rd.txt**, we can _less_ it and see what it reads. I want to show you that we cannot access it via the URL like our **Sup3rS3cretPickl3Ingred.txt** file. We need to use the command panel!

![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/er30rsrqr5cgohqpnsjb.png)
```
sudo less /root/3rd.txt
```
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hhvugok7sjoqjli04ksv.png)

Woohootie! We've found all three flags! 🥳
![CTF Page](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/svdkxuzmyhqctta3lc06.png)

---

## Conclusion
Hopefully Rick can return to human, thanks to us of course. I hope this write-up, however messy it was, helped somewhat. 

And with that, we have successfully completed the Pickle Rick CTF! 👾 
 

 



 
 

 
