---
title: "Day4"
date: 2023-08-29T23:35:36-06:00
---
Today I started working on the linux privilege escalation module and things were going swimmingly! Until i tried to load the exploit into the database per the [instructions](https://tryhackme.com/room/linuxprivesc): ![day4.1.png](/photos/day4.1.png)

Given that this setback was encountered at the beginning of the room, I felt confident that re-running the machine would fix any errors... so I terminated all the boxes and restarted them from scratch. Again, I started going through the directions, double checking all of my spelling and yet... it still didnt work:
![day4.2.png](/photos/day4.2.png)

After an extensive bout of googling, I found this very [helpful guide](https://juggernaut-sec.com/mysql-user-defined-functions/) which suggests that I try running this command:
![day4.3.png](/photos/day4.3.png)

...however as you can see above it couldn't find any such file or directory. Hmm. Opening up another shell instance, I navigated to the tmp directory and found...

![day4.4.png](/photos/day4.4.png)

useless... At this point, I've used up my free attackbox time and will continue to attack the box tomorrow with fresh eyes. 

