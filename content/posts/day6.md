---
title: "Day6"
date: 2023-08-31T23:54:35-06:00
draft: false
---
Today I embarked on the quest of installing Arch Linux upon a colleague's challenge.

I began by downloading an ISO from the Arch [website](https://archlinux.org/). Once I had that downloaded, I configured my Virtualbox with the settings below:

![day6.1.png](/photos/day6/day6.1.png)

I wasn't able to 
 
```
ping google.com
```

However I was able to 
```
ping 1.1.1.1
ping 8.8.8.8
```

Therefore I figured I had an issue with the DNS server and decided to continue on. I set the time and date, found the drive I wanted to partition, and set about partitioning the drive.

```
timedatectl set-ntp true
lsblk
cfdisk /dev/sda
```
At this point I was prompted to choose which label type I wanted to apply to my drive. 

![day6.2.png](/photos/day6/day6.2.png)

Confident I was following the tutorial properly, I selected gpt and moved on. This would come into play later on...

I gave my boot sector 128mb since we're only using Grub and gave sda2 the rest of the partitioned space. 

```
mkfs.ext4 /dev/sda1
mkfs.ext4 /dev/sda2
```

Brilliant, the filesystems were made without a hitch.

```
mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
lsblk
```
![day6.3.png](/photos/day6/day6.3.png)

Everything looks good so far. Time to use pacstrap to get the OS installed.

```
pacstrap /mnt base base-devel linux linux-firmware vim nano
```

Once this command is done, I made an fstab file using the /dev/sda filepath to ensure forwards compaibility should the drive UUID change in the future due to replacing a drive or some other reason.

```
genfstab /mnt
genfstab -U /mnt >> /mnt/etc/fstab
genfstab -U /mnt
```

This should give you an output with the /dev/sda options selected rather than the UUID. Now we can create root, double check that the directories look good, and use pacman to continue with the installation.

```
arch-chroot /mnt /bin/bash
pwd
ls
pacman -S networkmanager grub
```
 
Let's make sure NetworkManager works on boot

```
systemctl enable NetworkManager
```

Here we get Grub set up. 

```
grub-install /dev/sda
```

But there's an issue! Remember when I selected GPT instead of DOS? I got the error "grub-install: error: efibootmgr failed to register the boot entry: no such file or directory." and couldn't believe my eyes. I sprang to google, the arch forms, and stackoverflow but nothing they suggested in the past worked for me now. Figuring I had made a mistake, I went back through the tutorial again, making sure I followed closely this time and selected DOS instead. Now, when I got to installing grub it went without a hitch and I was able to carry on.

```
grub-mkconfig -o /boot/grub/crub.cfg
```

I set a password and language: 

```
passwd
nano /etc/locale.gen
```

Once I uncomment the en_US.UTF-8 and en_US ISO-8859 entries I have selected the language!

```
locale-gen
nano /etc/locale.conf
```

Here I set the generated language as English: 

```
LANG=en_US.UTF-8
```

Then set the hostname: 

```
nano /etc/hostname
Arch
```

Finally I set the timezone: 

```
ln -sf /usr/share/zoneinfo/America/Denver /etc/localtime
```

Exited the chroot, unmounted root and boot:

```
exit
umount -R /mnt
reboot
```

And after I logged in with the password I had set earlier I was in!

![day6.4.png](/photos/day6/day6.4.png)

Mission success.

