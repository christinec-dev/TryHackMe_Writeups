# Try Hack Me: Windows Privesc Complete Write-up
I'm not going to lie to you, sometimes I feel like the Try Hack Me labs are poorly written. Not in the way that I can't understand anything, but to the point where I feel like the author of the lab assumed that every student doing the lab will be on their level. That's where write-ups and walk-through videos make things easier. It helps beginners like me understand the fundamentals, and navigate my way through my hacker journey without getting frustrated, or giving up.

Today I thought to write my own write-up on a lab that I found pretty challenging: [Windows PrivEsc on the Jr Penetration Tester](https://tryhackme.com/room/winprivesc) learning path. Let's get started! 😊

**<u>I will be skipping over the following tasks since it is read-only to complete:</u>**
- Task 1: Introduction
- Task 3: Tools of the trade 
- Task 7: Token Impersonation 
- Task 8: Quick Wins

## Task 3: Information Gathering 
To read the instructions/general information for this section please go to the task dedicated to it. I will not be copying and pasting all the information since it will make the write-up bloated and honestly, if you're following along, it's unnecessary to repeat what you've already read! 😁

**<u>Launch the target machine.</u>**
We will need to use Attackbox for this, or our own Windows Machine. For this writeup, I will be working on Attackbox.
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/e0f7wj5ffhgdjef9kxzn.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8bwmyxgmqd3c8x52a2n7.png)
 
**<u>List users on the target system. One of them resembles a flag. </u>**
Open up your **cmd** on your Windows machine and enter in the `net users` command. We can see the flag clear as day!
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/11cdfr6k8gfw7k78fbn3.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/u0s8i6lj1g4sfqbi84hz.png)

**<u>What is the OS version of the target machine? </u>**
In your cmd enter the `systeminfo`. You will find the OS version next to OS Version. 👀
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ujepnj4vmgbbdi13m5ab.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/q59kliw32ytr032oafw9.png)
 
**<u>When was security update KB4562562 installed?</u>**
You can use the` wmic qfe get Caption,Description,HotFixID,InstalledOn command in cmd`, but an easier method to find the date for this is to open up PowerShell on your Windows Machine and enter the `Get-Hotfix` command. 
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sx1v87bbwmnie4zcfh9t.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4sy279pd4dv2ln2vtkpu.png)

**<u>What is the state of Windows Defender?</u>**
Go back into your normal cmd and enter the `sc query windefend` command. (This answer was in the sections' tutorial)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l0uebgcolchkpk2u37hv.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/89oh9smbxsec5eigzuqp.png)
  
## Task 4: Information Gathering   
**<u> What version of a Fitbit application can you see installed?</u>**
Go into your cmd and enter the following command `wmic product where "Vendor like %Fitbit%'" get name,version`. 
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ap4f8opyhry0znu13xxg.png)

You can also just go into your add/remove programs on your Windows Machine to reveal the same information.
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ncj0ghbn7qbrmwieq1ew.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/td3uvusrnkq05yww6zy2.png)

**<u> What kind of vulnerability seems to affect the Fitbit application?</u>**
This requires us to google a little bit. With a quick search on Exploit-DB we can see that it is affected by a Unquoted Service Path vulnerability.
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/g8zf6uhkygw689xfnb1y.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/mekh85pxy0wmpo08ryw5.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6euz0upby6y4a07fcf10.png)
 
**<u>What version of FoxitReader is installed on the target  system?</u>** 
If we try to use the `wmic get product name,version` command we can see that it does not return the FoxitReader service.
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9yo6bmle0860ha0m9tpd.png)

We will need to do something different. Go back to your cmd and enter the `wmic service list brief` command. You will see it listed below without it's version.
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/z43l60kmb7l01581p8q5.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h2px37zaqgi5kgmva2k8.png)

We know it's on the system, so we can go find it in our Program Files (x86) and read its properties to see which product version it's running.
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nv8em6q53jxtyfr7iz4o.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/317vejvh50ekg83z5e4q.png)
 
## Task 5: DLL Hijacking 
I'm going to be honest with you, this was way too much effort for the result received. If you want a detailed walk-through on this, let me know, but for now, I'm just going to go ahead and give you the answers. 😊

You can do this easily by just following the steps above and by making use of the hints provided. (The video that helped me with this task can be found [here](https://www.youtube.com/watch?v=VlRox0GmOzU)).

**<u>Login with Jack's account (the new password you have set). What is the content of the flagdll.txt file? </u>** 
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8t05hyksofspvfyghaly.png)

## Task 6: Unquoted Service Path 
**<u>What is the full unquoted path of unquotedsvc </u>** 
Open up your cmd and enter the `sc qc unquotedsvc` command to find the full unquoted path of unquotedsvs.
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2vuufck441atej0rjo21.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4uph64hryrl24wg0aqa0.png)

**<u>Go through subfolders in the unquotedsvc binary path. Which folder does the user have read and write privileges on? (Please write the whole path)</u>** 
Go to the C:\Program Files\Unquoted Path Service\ directory and read the properties of the folder. Stop before you go into Common Files.
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zx6j3ozwzs2slhaflerk.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/itkxx68o3aunzcj043sw.png)
  
**<u>What would be the name of the executable you would place in that folder? </u>** 
We can assume since it has a folder Common Files, the executable will be common.exe.

![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3fwxkvtckinjyqefnfcf.png)
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rqsgw2yedjnwp8unl73o.png)
 
**<u>Obtain Administrator privileges on the target machine. What is the content of the flagUSP.txt file?  </u>** 
Since this is another section where we need to set up a remote desktop client, I'm going to skip over that and just give the answer. If you want a detailed walk-through on this, let me know and I will add it in.

The [video](https://www.youtube.com/watch?v=VlRox0GmOzU) listed above really helped me with this.
![Windows Privesc](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0clntbblqjpcdnv8kg29.png)
 
## Conclusion
I hope this helped somewhat as this lab really tested me. I found it hard, and setting up the remote desktop was so much effort. The instructions (in my opinion) was also not as clear as I wished it would be, especially for task 5 and 6. Anyway, I got through it and now, so have you! 😀


 
 
