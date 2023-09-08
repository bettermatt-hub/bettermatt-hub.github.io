---
title: "Day9"
date: 2023-09-05T23:58:09-06:00
draft: false
---

Today I didn't have as much time as usual to work on my studies but I still kept my streak up. 

I started by finding all the SUID/SGID executables on the vulnerable machine: 
```
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null
```

Then utilized cve 2016 1531 to exploit /usr/sbin/exim-4.84-3
```
/home/user/tools/suid/exim/cve-2016-1531.sh
```
I was in!

On a slightly different topic, rememeber that arch machine I started playing with? I finally installed a gui (XFCE) and yay (AUR helper)! 

Getting xfce up and running was a breeze, I simply ran hitting enter throughout the way to accept any dialogues): 
```
pacman -Syuu
pacman -S xfce4 xfce4-goodies
pacman -S lightdm lightdm-gtk-greeter
systemctl enable lightdm
reboot
```

After my VM had finished rebooting, I was greeted by the simple yet pleasant login screen. When installing yay, I had some trouble, as running
```
pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

Denied me access to makepkg as root. I spun up a new user to use that to run the makepkg command, however since I'd downloaded yay as root it wouldn't let me do it. I ended up removing the downloaded directory and restarted the process as the new user
```
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

Which worked! I was finally able to install a web browser and any other items I needed from the AUR. 
