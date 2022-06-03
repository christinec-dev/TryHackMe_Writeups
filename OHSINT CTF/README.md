# OhSINT! Complete CTF Writeup for Try Hack Me Beginners
Imagine a world where you can cyber stalk someone you barely know and not seem like a weirdo. Well, in today's CTF writeup you can! The [OhSINT CTF on Try Hack Me](https://tryhackme.com/room/ohsint) allows you to put your stalking skills to use, and it all starts with a picture. 📸

First things first. Open-source intelligence (OSINT) (or oh shi-) refers to the collection and analysis of data gathered from open sources such as sources found over the internet to produce actionable intelligence on a subject. 

Okay, gone with you boring information. Let's start hunting!

## Task 1: OhSINT 
Before we begin with answering the questions, let's download the task files which contains our image. Once you've loaded up your image, right click and open up its properties. When we go into the details, we can identfify the author of the image: `OWoodflint`.

![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/937zdr6cn9u0g0nbjul8.png)
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qz0dhhy7d3husaxx22bu.png)

Let's see what we can find about our new friend OWoodflint by Googling them. We can see that OWoodflint has three profiles: [Twitter](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwj_56Hr9JH4AhV5QUEAHUeGBIwQFnoECAsQAQ&url=https%3A%2F%2Ftwitter.com%2Fowoodflint%3Flang%3Den&usg=AOvVaw2LXO6BTvT5Yl78OAUo4R7c), [GitHub](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwj_56Hr9JH4AhV5QUEAHUeGBIwQFnoECAgQAQ&url=https%3A%2F%2Fgithub.com%2FOWoodfl1nt%2Fpeople_finder&usg=AOvVaw2D0n2sajxpplNg-ykWFFKc), and a [personal blog](https://oliverwoodflint.wordpress.com/author/owoodflint/).

![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1b4vjjmko4sanzee8wk7.png)

### OWoodflint's Twitter
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fugvf5whq1d97ls3g9qu.png)

### OWoodflint's GitHub
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4xfcblouzwbysxix8b9y.png)

### OWoodflint's Blog
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bvnt3czfzgxnmc2gxeem.png)

Now that our basic hunting is over, let's start answering questions!

**<u>What is this users avatar of?</u>**
Let's have a look at their Twitter. It's obviously a racoon. 🦝
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/v22gk4xxlifd6vh9a3ri.png)
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/g0nr8qgzegw69cd0k410.png)
 
**<u>What city is this person in?</u>**
If we go over to their GitHub, we can see that they stated in their README.md that they are from London.
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wmtieupl0snyuytey0yj.png)
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kjz5wfybzk46ljp1eqo5.png)
 
**<u>Whats the SSID of the WAP he connected to?</u>**
When we go over to their Twitter we can see that they made a tweet about their "free wifi". We can use this BSSID to find the SSID of the WAP they connected to.
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cjjmhm1fnxhlfh1pt49t.png)
 
To do this, we need to go over to [Wigle.net](https://www.wigle.net/). Register for an account, and once you've done that head over into view > advanced search. Paste in the BSSID they tweeted and let the good times roll. 😎
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7yj9jgssnucl1mbve19t.png)
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3if2qrjzg9iu0kcsvet5.png)
 
**<u>What is his personal email address?</u>**
Back to their GitHub profile and all will be revealed.
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4xfcblouzwbysxix8b9y.png) 
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hu66ldu0ah36wtb6was7.png)

**<u>What site did you find his email address on?</u>**
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/b4fr7kgz1pmenejnltvj.png)

**<u>What site did you find his email address on?</u>**
When we head over to their blog, we can see that they recently went on holiday to New York.
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pxihkhq0o13fcajwz5zh.png)
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/il1y1ly9pfd06w8w60f6.png)

**<u>What is this persons password?</u>**
For this we need to view the page source of their website. You will find the answer to this in a hidden <p> tag underneath their intro paragraph.

![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tmhzlgo459fw2xti974s.png)
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zsqmj0p2871kwgf72nwk.png)
  
## Conclusion
Congratulations! You are now a certified stalker(this is a joke, OSINT is very useful and unlike stalking, legal). I made you a certificate because you deserve it:
![OhSINT](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ics6phuyaqwmlew03264.png)

See you next time. Happy hacking! 😊

 
 
 

 
 

 

