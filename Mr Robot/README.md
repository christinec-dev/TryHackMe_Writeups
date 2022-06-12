# Let's Hack The World in The MR. Robot CTF! 👾

Today we are going to take a crack at the [Mr. Robot CTF](https://tryhackme.com/room/mrrobot) on Try Hack Me. I must say before we start that I love the design of this lab! The website is so cool, and so well thought out, it was just perfect. I really encourage you to look at all the videos, it's pretty _hackery_.😀 

When you're ready, put on your **FSOCIETY** hoodie and let's hack the world!

---

## What is key 1?
Once your machine is loaded, we can start with our basic enumeration. First things first, when we open up the IP address of the machine in our browser we are met with a command-line like website. Each command that you type in will load a video, so it's not really that important (but you can check it out if you want). 

![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qqe7qiske76puqo3qi60.png)

Let's run an **nmap** scan to see if we can find any services.
`nmap -sV -Pn <your machine IP>`
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/e6wridgxi3i00sr6oxb8.png)

Mhh, we can see that our ssh port is closed. There is a ssl/http port that is of interest though. Let's run a **gobuster** scan to see which directories we can enumerate.
`gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -u <your machine IP> -t 50`
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ufj2ag0431mo2x3g9jdm.png)

We can see that there is a `/robots` directory. When we look at our hint it says "Robots". Let's navigate to our <ip>/robots.
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/i3n2orlr43f4e8140zlm.png)
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/iryss282ik3ou982d3uh.png)
 
We can see that our robots.txt mentions a `key-1-of-3.txt` file. It also has a `fsocity.dic` file which contains a list of passwords. Save this file because we will need it later. Let's navigate to it: `<ip>/key-1-of-3.txt`.
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xzej8a8ozrhqhr1fvtyq.png)
 

We've found our first key!
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/y0rernio1cw8x9df3y9s.png)
 
---

## What is key 2?
When we look back at our gobuster scan, we can see that there is a /login and /wp-login directory that indicates that the site is made with Wordpress. Let's navigate to our <ip>/wp-login.
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tm0a1b9j5q5cfyolro3e.png)

We won't get pretty far without a username and password (duh!). If we run a **wpscan** scan on our web application to see if we can find a user, we can see that we get nothing useful (except the Wordpress version which we could maybe exploit). 
`wpscan --url http://<your machine IP>/wp-login --enumerate u`
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5kkbalwqgu5zhinxzanh.png)
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9b43n736qqsxappnzjt5.png)

We have one of two options now: manually trying different usernames or making use of Burp Suite's intruder to find a username. When I first did this CTF, I tried my luck by going with the most obvious usernames: MrRobot and Elliot. `Elliot` won! 
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0axe79zkxp9gmlvjev8x.png)
 
Now that we have a username, we can go back to wpscan (or in my case I chose [hydra](https://github.com/frizb/Hydra-Cheatsheet)) and enumerate through our fsocity.dic file that we downloaded above to find a valid password for Elliot. 
- `export ip=<your machine IP>`
- `hydra -l Elliot -P ./Downloads/fsocity.dic $ip -V http-form-post '/wp.login.php:log^USER^PASS^&wp-submit=Log In&testcookie=1:S=Location'`
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a1nhrglhkk2syytgt6v6.png)

This took my scanner 40 minutes, so to save you the effort I will reveal the password for you >> **Elliot:ER28-0652**. Let's log in using these credentials!
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xm4toh79camkttos1nmp.png)
 
From here on, our next moves are pretty standard. Let's see if we can run a reverse shell using php by pasting our shell in the **404.php** file in the theme editor. Remember to save this new file. You can download the reverse shell from [pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell), and remember to update the IP address with the address of your OPENVPN (not your machine IP!) and insert the port of your choice (I left it at 1234).
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/09v83c0xcks4jm47fgw9.png)
 ![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zb7jna3x1ml9h2orz393.png)
 
Start up a netcat listener.
`nc -nlvp <your port insterted in reverse shell>`
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6zpbjhp2xhzp91kdbm6p.png)
 
Now, head over to `<your machine IP>/404.php` and check your netcat listener. We have successfully gained access via our reverse shell!
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xz5a5momwtw80xjft99b.png)
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vt04v8pe2k2638wc94tp.png)
 
When we list the files of /home/robot, we find our key-2-of-3.txt file. There is also a password.raw.md5 file - which we'll get to later.
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8rc6ur8t2z51irqv76g6.png)

We have our second key! 
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4z8wnllskhnwd91rr1z5.png)
   
---

## What is key 3?
Let's read the contents of our `password.raw.md5` file. It seems to be a hashed password for the user robot.
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/95bdqegg6lhogsnee54a.png)

Let's see if we can crack this password hash. Head over to [Crackstation](https://crackstation.net/) and enter this hash.
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fi8x1a5kw41iv3cfwo81.png)
 
Let's see what user we are currently by running `whoami`. We are running as daemon, but we can log into the user robot's account since we have the password (the one we just cracked)!
- `whoami`
- `su robot`
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d7u2dunv8jrk5zbpp20q.png)

Now that we are logged in as robot, let's then see what binaries we can access(to see if we can escalate our privilege via exploiting our binary library). 
`find / -perm -u=s -type f 2>/dev/null`
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a6we58nmw4x3c3m2uozx.png)

The binary /nmap looks prmising. Head over to [GTFObins](https://gtfobins.github.io/gtfobins/nmap/) and read up on how we can escalate our privilege using this library. We need to run an interactive shell which will give us root access.
`nmap --interactive`
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/k31o8yixco0zi516s46l.png)

From here on we can cd into root, and read the contents of our final flag file: `key3-of-3.txt`.
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/p9o784u1z9arm5sambcg.png)
 
And so we got our flag!
![Mr Robot CTF](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/aylqxwyamkakbkimxdr3.png)
 
---

## Conclusion
There's a bunch of steps I took in between that didn't pan out into anything. There weren't any vulnerabilities for the Wordpress version, in case you were wondering. Ultimately, it was quite an easy CTF! 😁

![Mr Robot CTF](https://media.giphy.com/media/ZKQpx4TYrxTtS/giphy.gif)

I hope this was easy enough for you to follow, and until next time, happy hacking!






