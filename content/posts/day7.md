---
title: "Day7"
date: 2023-09-03T21:07:27-06:00
draft: false
---
Today I revisited that linux privesc that gave me so much trouble on day 5. I went through the whole module again and had the same issue as last time! After restarting the machine, I realized that I was having a lot of trouble with this command: 

![day7.1.png](/photos/day7/day7.1.png)

Instead of typing it out manually by hand, I just copied and pasted it into the terminal... and it worked!

![day7.2.png](/photos/day7/day7.2.png)

With that out of the way, the rest of the module went swimmingly and I was able to get into root without the password: 

![day7.3.png](/photos/day7/day7.3.png)

Great! On to the next one. I found the hashed root password in the /etc/shadow file: 

![day7.4.png](/photos/day7/day7.4.png)

Then I copied that over to my attackbox where I used John The Ripper to quickly find the password:

![day7.5.png](/photos/day7/day7.5.png)

After using the found password123, I was able to access root with no trouble:

![day7.6.png](/photos/day7/day7.6.png)

This next one was simply mismanaged permissions. I saw that I had write permissions for /etc/shadow and was able to change the hash of the root password to one of my choosing. Once I fixed my syntax and saved the file, switching to root was a piece of cake!: 

![day7.7.png](/photos/day7/day7.7.png)


