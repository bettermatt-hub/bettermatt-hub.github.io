---
title: "Day8"
date: 2023-09-04T23:58:09-06:00
draft: false
---

Today I went through a bunch more Linux privesc modules and I'm about halfway done with the room at this point. 

The first one I did was attacking the root password hash by reading the /etc/shadow file as a standard user. I saw that the permissions allowed me to read the file:
```
ls -l /etc/shadow
cat /etc/shadow
```

I was able to find the root hash and copy it over to my Kali attackbox. I loaded it into a text file called hash.txt and then ran the following command to crack the hash using John the Ripper:
```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

At that point I was able to switch to root and simply log in using the found password. 

For the next attack, I noticed that I was also able to write to the /etc/shadow file in the previous attack. At that point I was able to generate a new password hash using:
```
mkpasswd -m sha-512 newpass
```

Then replace the root password hash with the one I'd generated, logging into root with newpass.

Next, I checked the permissions on the /etc/passwd, which was mistakenly configured to allow me write access. I generated a new password with:
```
openssl passwd passnew
```
Afterwards, I simply replaced root's hash, again allowing me access. Alternatively, you could create a newroot user in the /etc/passwd file and paste that hash there, giving you a whole newroot user.

The next couple of modules were slightly different, utilizing the [GTFOBins](https://gtfobins.github.io) repository of exploits to privesc with 10 of the 11 available sudo commands! I first used sudo -l to see which were available to me and then searched each of those commands up on GTFOBins to see how to break into a root shell. 

Finally, I used environment variables to exploit preloaded shared objects and libraries. 

I created a shared object using: 
```
gcc -fPIC -shared -nostartfiles -o /tmp/preload.so /home/user/tools/sudo/preload.c
```

Ran it via one of the allowed sudo commands:
```
sudo LD_PRELOAD=/tmp/preload.so man
```

Allowing me to access a root shell. Then I ran ldd to check which shared libraries are used by apache2
```
ldd /usr/sbin/apache2
```

Created a shared object using libcrypt.so.1 (one of the libraries listed):
```
gcc -o /tmp/libcrypt.so.1 -shared -fPIC /home/user/tools/sudo/library_path.c
```

Running apache2 using sudo with the shared library subsequently spawned a root shell and I was in!
```
sudo LD_LIBRARY_PATH=/tmp apache2
```

