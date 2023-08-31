---
title: "Day5"
date: 2023-08-30T23:10:12-06:00
---
Today, I went through the Cron Job privesc module as I'm studying up on my Linux for an important interview I've got in the morning. I really liked these, it shows how you can use legitimate, built in processes within Linux in order to get access to directories you wouldn't ordinairly be able to access. The first one was simple, once I setup a ssh connection to the vulnerable machine I looked at the existing cronjobs, found one that was able to be written to, and inserted some code to connect a root shell back to my attackbox. 

![day5.1.png](/photos/day5.1.png)
![day5.2.png](/photos/day5.2.png)

For the next one, I created a file called overwrite.sh in the user directory and gave it the following contents:

![day5.3.png](/photos/day5.3.png)

I made it executable and then waited a mintue for the cron job to run and then I was able to access the root user:

![day5.4.png](/photos/day5.4.png)

![day5.5.png](/photos/day5.5.png)

After I cleaned up that file and switched back to the standard user, I used msfvenom on my attackbox to generate a reverse shell ELF binary and sent it to the vulnerable machine: 

![day5.6.png](/photos/day5.6.png)

![day5.7.png](/photos/day5.7.png)

I gave it execute permission and then when the tar command in the cron job ran, it recognized those files as tar command line options rather than plain files and connected to my attackbox:

![day5.8.png](/photos/day5.8.png)

 See this [page](https://gtfobins.github.io/) for more info on that exploit.
 
 This just goes to show that having properly configured permissions, locking down unused ports, and minimizing attack surface is important system wide, especially in automated processes. 
