---
date: 2021-02-14
layout: post
title: RAID on the Ubuntu Server Live installer
...

My first contact with Ubuntu was in 2006, a little after the first Long-Term Support (LTS) version [6.06 (Dapper Drake)][dapper] was out. Although it still feels like yesterday, 15 years is a heck of a long time. Things were a bit different by then, as the Canonical LTS offer was of about 3 years on desktop and 5 years on server releases - instead of 5 years for both as it stands to this date. They even sent free CDs to anyone in the world, including shipping, from 2005 to [2011 when the initiative was ended][free-cds-end]. This may look stupid now, but downloading a CD over a 56k dial-up connection (which was still a thing in multiple parts of the world) [used to take over a day][cd-over-dial-up]. Even ADSL connections were not that much faster, as the most common ones were around 256-300 Kbps.

It took me a few more years to use Linux on a desktop, which [I did around the end of 2012][linux-desktop], although I was using it on my servers at least since 2010 - the year I started to grab [cheap VPS offers from LowEndBox][leb]. By 2013 [I started to work][minassoft] with [Herberth Amaral][amaral] (which is also one of the most competent professionals I know), where Ubuntu was being used on the servers instead of Debian - the latter being Linux distribution I was used to. That didn't make a huge difference, as both are quite similar when you don't consider their desktop UI, but I still opted for Debian on my own machines.

This trend continued when [I started to contribute to the Debian Project in 2014][gsoc], where I used a Debian server as my primary development machine. But, except for this server that I still have 7 years later, almost every other server I had or company that I worked on used Ubuntu - except for one employee that used CentOS. So by the end of last year when I realized that this machine wasn't getting security updates for almost six months [since the Debian Stretch support was ended][debian-support], I started to think why not just install Ubuntu on it. By doing that, I could forget about this machine for 5 more years until the LTS support ended.

To be fair, to say that I use Ubuntu on "almost every other server" is an understatement. Ubuntu is my go-to OS option on almost every kind of computing environment I use - except for my desktop which is a macOS since 2016. Ubuntu is the OS I use when starting a virtual machine with `vagrant up`, an EC2 instance on AWS or when I want to try something quick with `docker run` (although I use [Alpine Linux][alpine] frequently in this last use case). So opting for it on a server that is going to run for at least a few more years felt like a natural choice to me - at least until I faced their new server installer.

To give a bit of context, by being a Debian-based distribution, Ubuntu used the regular [Debian Installer][d-i] for its server distribution until the 18.04 (Bionic Beaver) LTS release, when [it introduced the Ubuntu Server Live Installer][live-server-intro]. It didn't work for me, as by the time it didn't support non-standard setups like RAID and encrypted LVM. This wasn't a big deal, as it was quite easy to find [ISOs with the classic installer][bionic-classic], so I ignored this issue for a bit. The old setup offered the features I needed and my expectation was that it was a matter of time for the new installer to be mature enough to properly replace the former.

Last year the new Ubuntu 20.04 (Focal Fossa) LTS release came, where the developers [considered the installer transition to be complete][live-server-finished]. The notes mention the features I missed, so I thought that it would be a good idea to try it out. So let's see how a RAID-0 installation pans out:

![][raid0-ubuntu]

Wait, what?! What do you mean by `If you put all disks into RAIDs or LVM VGs, there will be nowhere to put the boot partition`? GRUB supports booting from RAID devices [at least since 2008][grub-raid], so I guess it's reasonable to expect that a Linux distribution installer won't complain about that 13 years later. To make sure I'm not crazy or being betrayed by my own memory, I tried the same on a Debian Buster installation:

![][raid0-debian]

No complaints, no error messages. The installation went all the way and booted fine in the end. "Something is odd", I thought. By comparing the two partitioning summaries, I noticed that the Debian one is using partitions as a base for the RAID setup, while the Ubuntu one is relying on the entire disks. I went back to the Ubuntu installer and tried to use similar steps. The problem now is that if the option `Add GPT Partition` is used for both devices, it creates two partitions on the first disk and only one on the second disk. So I dropped to a shell from the Live Server installer with `ALT + F2` and manually created empty partitions on both disks with `fdisk` (`cfdisk` will do fine as well). After a reboot, I tried again:

![][raid0-ubuntu-part]

Well, the complaint went away. But after creating the RAID array, opting to format the newly device as `ext4` and choosing `/` for its mount point, the `Done` button was still grayed out. Looking at the top of the screen again, the `Mount a filesystem at /` item was gone, so the last one that needed to be filled was the `Select a boot disk`. Clicking on one of the disks and selecting the option `Use As Boot Device` did the trick.


[alpine]: https://alpinelinux.org/
[amaral]: https://github.com/herberthamaral
[bionic-classic]: https://askubuntu.com/a/1035097
[cd-over-dial-up]: https://www.wolframalpha.com/input/?i=700MB+at+56Kbps
[d-i]: https://wiki.debian.org/DebianInstaller/
[dapper]: https://en.wikipedia.org/wiki/Ubuntu_version_history#Ubuntu_6.06_LTS_(Dapper_Drake)
[debian-support]: https://en.wikipedia.org/wiki/Debian_version_history#Release_table
[free-cds-end]: https://ubuntu.com/blog/shipit-comes-to-an-end
[grub-raid]: https://lists.gnu.org/archive/html/grub-devel/2008-10/msg00040.html
[gsoc]: /2014/04/o-dia-em-que-perdi-cinco-mil-dolares
[leb]: https://lowendbox.com/
[linux-desktop]: /2013/02/trocando-o-iceweasel-pelo-firefox
[live-server-finished]: https://discourse.ubuntu.com/t/server-installer-plans-for-20-04-lts/13631
[live-server-intro]: https://ubuntu.com/blog/updatable-ubuntu-server-live-installer
[minassoft]: /2013/01/traduzindo-apps-do-django
[raid0-debian]: /images/2021/raid0-debian.png
[raid0-ubuntu-part]: /images/2021/raid0-ubuntu-part.png
[raid0-ubuntu]: /images/2021/raid0-ubuntu.png
