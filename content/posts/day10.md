---
title: "Day10"
date: 2023-09-09T23:58:09-06:00
draft: false
---
Today I went through and finished the Linux privelege escalation module! The next 4 exploits are concerning SUID/SGID executables. 

Following the instructions, I began by running the `/usr/local/bin/suid-so` executable and saw it gave me a progress bar:

![day10.1.png](/photos/day10/day10.1.png)

I then ran strace on the file in order to run the program while logging the system calls it makes, searching for errors with grep.

![day10.2.png](/photos/day10/day10.2.png)

As you can see, it tries to load `/home/user/.config/libcalc.so` but it can't be found. I then created the .config directory, compiled it at the expected location, and executed the suid-so executable again, giving me a root shell. 

![day10.3.png](/photos/day10/day10.3.png)

Next, I tried executing `/usr/local/bin/suid-env` which tried to start the apache2 webserver. I ran strings on the file to look for a list of strings, noticing that while the service executable is being called, the full path isn't being used. I compiled the code located at `/home/user/tools/suid/service.c` (which spawns a root shell) into an executable called service, prepended the current directory to the PATH variable, and ran the executable to gain a root shell.

![day10.4.png](/photos/day10/day10.4.png)

The next one is quite similar, although rather than using the local path it uses the absolute path. After verifying this with the `strings` command, I checked the Bash version installed on the vulnerable machine. Since the version installed is < version 4.2-048, it's possible to define shell functions with file path names and then export that function so it's used instead of the expected executable. I created a Bash function named `/usr/sbin/service` that gives me a new bash shell and exported the function. Once I ran the suid-env2 executable, it gave me a root shell!

![day10.5.png](/photos/day10/day10.5.png)

On this exploit, things were going smoothly as I followed the tutorial, running the `/usr/local/bin/suid-env2` executable with bash debugging and PS4 set to an embedded command until:

![day10.6.png](/photos/day10/day10.6.png)

Oh no! I check the directory, expecting to see my rootbash file:

![day10.7.png](/photos/day10/day10.7.png)

Useless... not again:

![day10.8.png](/photos/day10/day10.8.png)

Copy and paste the command directly and it works! I mistakenly ran the command on two lines rather than on one as I should have, guess I had my screen zoomed in a bit much. This time it worked:

![day10.9.png](/photos/day10/day10.9.png)

For this exploit, I checked the hidden history files, looking for a password mistakenly typed into the command line:

![day10.10.png](/photos/day10/day10.10.png)

Found something:

![day10.11.png](/photos/day10/day10.11.png)

Switched to root using the found password... and I'm in:

![day10.12.png](/photos/day10/day10.12.png)

Now I checked the contents in the home directory and found auth-user-pass located at `/etc/openvpn/auth.txt`:

![day10.13.png](/photos/day10/day10.13.png)

Opening that text file, I was greeted by root credentials:

![day10.14.png](/photos/day10/day10.14.png)

Which I then used to sign in:

![day10.15.png](/photos/day10/day10.15.png)

Great! This next one I found a hidden directory called .ssh which contained a readable file called root_key.

![day10.17.png](/photos/day10/day10.17.png)

![day10.16.png](/photos/day10/day10.16.png)

Going to my attackbox, I created a file called root_key, pasted the key in and gave it the correct permissions, then passed it along to the vulnerable machine using ssh and was greeted with root access:

![day10.18.png](/photos/day10/day10.18.png)

For this next one, I'll be creating a mounted share point and sending an msfvenom payload using NFS to the vulnerable machine. I began by checking the /etc/exports file to see the NFS configuration:

![day10.19.png](/photos/day10/day10.19.png)

On my attackbox, I created the mount point and mounted the /tmp share, generated the payload, and made it executable:

![day10.20.png](/photos/day10/day10.20.png)

Then on the vulnerable machine I just executed the file and got a root shell:

![day10.21.png](/photos/day10/day10.21.png)

Finally, using the DirtyCow kernel exploit (CVE-2016-5195) I was able to gain a root shell quite easily. I used perl to find potential exploits:

![day10.22.png](/photos/day10/day10.22.png)

I had a couple of typos as I compiled the exploit:

![day10.23.png](/photos/day10/day10.23.png)

However after a couple of tries it worked! I then ran the `/usr/bin/passwd` and gained a root shell. 

![day10.24.png](/photos/day10/day10.24.png)

That was it and I completed the module!
