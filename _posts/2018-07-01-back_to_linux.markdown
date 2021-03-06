---
layout: post
comments: true
title: "Back to Linux!"
date: 2018-7-1 20:18:57
categories: tools
tags: [tools, Linux, Ubuntu, Arch]
excerpt: "Back to Linux! Here you have some notes from my migration to Ubuntu with a XPS 9570"
---

# Running away from Mac? How come?

_20190806 UPDATE: this guide was born for XPS 9570 and Ubuntu. Then it was updated for XPS 7590 and Arch Linux with LUKS._

_20200926 UPDATE: I lost many hours today trying to bring my installation back to work, I've added a postmortem with useful links._

I've been a Mac user for 6 years. Back in 2012 I had been a really happy Gentoo user for many years, but by then some iOS development ([a simple iOS "game"](http://www.juanignaciosl.com/ingenieria-del-software/the-exif-game) and a freelance project) moved me to OS X. In addition, I also [take pictures](https://www.flickr.com/photos/juanignaciosl/) and even [make videos from time to time](https://www.youtube.com/watch?v=EHoAyxU8nog&list=PL5Gftzmh1mO6iuWbsVU9cbzzszjcebKoj). So, switching to Apple was a nice move: I'd keep the good parts of a \*nix sistem and get rid of Windows for the Adobe suite. In addition, Macbook Air was a great computer for its price, powerful enough and extra light.

I used two Macbook Airs (personal and work) for 4 years. Past year, when I wanted a more powerful machine, I moved to the new Macbook Pro (yes, the one with the infamous Touchbar). A ~$3k device. It should've been great. Spoiler: it wasn't.

Here are _some_ of _my_ pains:

- The **Touchbar** is not a good addition. It provides no value at a cost: you miss the physical feedback of the buttons. I wouldn't have chosen it, but it's a must if you want a top of the line 15" device. The theoretical benefit is having a context-aware key set, but it just doesn't work. I haven't taken advantage of it a single time in a year and a half. Why would you look at the keyboard? For context-aware interface you already have the UI, and for quick functions you have shortcuts. Anyway, I wouldn't have got rid of it just because of this.
- The **infamous** keyboard. During the last weeks, 5 keys were malfunctioning. Really. Throughout my life I've used ~15-20 keyboards in a daily basis and this is the first time that a keyboard has been a problem. It's not even the first generation of the design, which was introduced for the Macbook, so there's no single excuse for this. At least, [Apple has conceded](https://www.apple.com/support/keyboard-service-program-for-macbook-and-macbook-pro/).
- In CARTO we use **Vagrant and Docker** A LOT, specially at backend. Performance is far from great in Mac, and Data team deal with large datasets, so this is a huge pain and source of frustration. It's not only slow: long tasks on USB drives with NTFS format would hang (I corrupted a PostgreSQL 4 times running batches), and a simple `rm -rf node_modules` made the system crash. This is arguably not a macOS issue, maybe it's just that Linux is a much better fit for me.
- Battery didn't last 4 hours with basic use. Not even 3h with videoconference.
- A lot of small annoyances:
  - For some reason, switching among tasks that were maximized to occupy full desktops (and I was a heavy user of that) stopped working.
  - Some that might not be _directly_ macOS fault, but wasn't mine either: Tunnelblick wouldn't deal with VPN DNSs properly (Ubuntu does, with a nice native UI).
  - HDMI support was horrible (at least with my monitor, until the last update.) For more than a year, plugging my external monitor always led to a blue screen that was only fixed by locking and unlocking (?).
  - My monitor has a USB Hub where a Ethernet adapter is plugged. 90% of the times that I plug the computer back I have to unplug and plug the adapter for it to work.
  - About the last 2 complaints: no, an official Apple hub didn't fix it either.
  - An additional external power adapter costs 90€, and it doesn't even includes the needed USB-C (which Apple sells at 30€).

So, I asked for a computer change. Other person in CARTO (designers? frontenders?) can enjoy the powerful Macbook Pro more than I would, and my life might be happier.

# XPS 9570

After some research, I chose the XPS 9570. In fact, I chose the 9560, but when I was going to ask for it the announcement of the new model was too close, so I preferred waiting :). XPSs are known as good machines for developers, with good Linux compatibility. Highlights:

- Did you know that a laptop can be thin enough and still keep USB 2 and HDMI ports? Mind-blowing!
- I admit that I wanted the higher resolution one, but 4k for a 15"4 laptop is certainly an overkill. FHD screen is nice enough and improves battery live.
- While it's slightly wider it's slightly "shorter" than the Macbook Pro, so the size is more or less equivalent. I use a vertical layout for my displays (it's feels better for my neck), placing the laptop below my main monitor, so those dimensions happen to be more comfortable to me.
- The webcam is placed under the screen. I guess that it makes the frame thinner. That's nice, but I look like [Jim Carrey at Bruce Almighty](https://media.giphy.com/media/11M1k4fIwVqPF6/giphy.gif) during the meetings. That might cause me a meme, but it's still a good tradeoff :). If I could upgrade my iPad to iOS 10 I'll try using it for videoconferences, but that's no longer true :(. BTW, 0 problems for webcam, sound or microphone. _It just works_.
- ~$1000 cheaper than the equivalent Macbook Pro.
- Keyboard seems to work, so far. Crazy.

## Ubuntu installation

Although I love Gentoo, I don't have too much time and my Linux skills are a little rusty, so I kept things simple and installed Ubuntu. There are plenty of guides for the 9560. [Read this first](https://github.com/rcasero/doc/wiki/Ubuntu-linux-on-Dell-XPS-15-(9560)), and [this](https://www.reddit.com/r/Dell/comments/8l7gk8/xps_15_9570_fhd_review_and_linux_instructions_and/), and other useful references:

- [XPS 9560 and Ubuntu](https://gist.github.com/tomwwright/f88e2ddb344cf99f299935e1312da880).
- [DELL XPS 9560 - The Perfect Linux Laptop?](https://www.youtube.com/watch?time_continue=5&v=GiM05IkB1Qc) video series + [the Full List of Changes](http://www.palindrome.co.uk/p/blog-page_23.html).

## Non-obvious steps

1. BIOS
    1. [Change SATA Operation from "RAID On" to "AHCI"](https://github.com/rcasero/doc/wiki/Ubuntu-linux-on-Dell-XPS-15-(9560)), including restart into safe mode.
    2. Disable Secure Boot --> Secure Boot Enable.
    3. Enable General --> Advanced Boot Options --> Enable Legacy Option ROMs.
    4. POST Behavior --> Fastboot --> Thorough.

2. Windows:
    1. Turn off device encryption. I'm not 100% sure if this is needed, but I think it is if you want Ubuntu to be able to find the drive.
    2. Resize partition to make room for Ubuntu.

3. Ubuntu installation:
    1. When you restart the computer, after F12, make sure you use "UEFI BOOT" alternative so the Windows drive is properly detected.
    2. (Sometimes? this hasn't been necessary) Click a key when you see some icons in the bottom and added `nouveau.modeset=0` to the boot options (see [1](https://github.com/rcasero/doc/issues/6), [2](https://help.ubuntu.com/community/BootOptions)).

## First boot

After user and password, I got an empty desktop and a frozen cursor. CTRL + Alt + F2 allowed me to run this fix: [1](https://askubuntu.com/questions/779595/ubuntu-16-04-lts-black-screen-after-login), [2](https://askubuntu.com/questions/1030060/freeze-after-login-ubuntu-18-04):

```bash
  sudo dpkg --configure -a
  sudo apt-get update
  sudo apt-get upgrade -y
  sudo add-apt-repository ppa:graphics-drivers/ppa
  sudo apt-get install nvidia-390
  sudo reboot
```

In addition, this also fixes an issue that wouldn't allow Ubuntu to shutdown or reboot.

## Suspend mode

Read [this](https://askubuntu.com/questions/1029474/ubuntu-18-04-dell-xps13-9370-no-longer-suspends-on-lid-close?rq=1) and [this](https://www.dell.com/community/XPS/XPS-15-9570-and-Ubuntu/td-p/6096817) to learn a couple of things about sleep levels. In addition to that, I had to [expand swap size beyond my RAM](https://help.ubuntu.com/community/SwapFaq#How_do_I_add_a_swap_file.3F). With that and `mem_sleep_default=deep` at `/etc/default/grub` I have a working suspend mode that wastes almost no energy.

## Energy management

As usual, graphic card drivers are a pain. As I don't take advantage of the nVidia card at Linux\*, I chose to power it off by [removing nouveau module](https://askubuntu.com/questions/841876/how-to-disable-nouveau-kernel-driver). [I also set powertop to autotune on boot](https://blog.sleeplessbeastie.eu/2015/08/10/how-to-set-all-tunable-powertop-options-at-system-boot/). Although I haven't tested it yet, that should grant ~10h of battery autonomy.

_\*: this might change soon, as I'm considering giving Darktable a try._

## Software

[This is my accumulated `apt install` so far](https://github.com/juanignaciosl/configuration_files/blob/365260135f7c675e5a8e378eea74b2e7d398453a/setup.sh#L16).

For Docker to work without `sudo` you need this:

```bash
sudo usermod -aG docker USERNAME
```

Vagrant needed manual installation because of an issue ([1](https://github.com/dotless-de/vagrant-vbguest/issues/292), [2](https://github.com/mitchellh/vagrant-aws/issues/541)).

IntelliJ is my main development environment. Although it's available through Snap (`sudo snap install intellij-idea-ultimate --classic --edge`) you still need JetBrains Toolbox for license management and sync settings, so it's not useful enough. In addition, Toolbox is a nice launcher that helps picking recent projects.

Spotify, though, can be installed with Snap: `sudo snap install spotify`.

_Pro-tip: edit `/home/USERNAME/snap/spotify/16/.config/spotify/Users/USERNAME-user/prefs` and add `storage.size=1024` to reduce disk usage from 10GB to 1GB._

I used Linux for years, but I moved to Mac ~7 years ago. Although some of the configuration files (such as `.vimrc`) might be taken from my old [`.dotfiles`](https://github.com/juanignaciosl/dotfiles) repo, I created a new, simpler [configuration files](https://github.com/juanignaciosl/configuration_files) repo from scratch.

I'm trying [Lepton](https://github.com/hackjutsu/Lepton) as a Github Gists client.

Extra ball: [some suggestions](https://dev.to/brpaz/my-linux-development-environment-of-2018-ch7), via [@elmendalerenda](https://twitter.com/elmendalerenda), thanks!

## Sound output disappearing

From time to time there's no sound and only "Dummy output" appears at sound configuration. Running `pulseaudio -k && sudo alsa force-reload` usually makes it work again.

## WiFi disconnecting

From time to time WiFi stopped working because of a driver crash (sorry about not pasting syslog output). [I just followed the manual firmware update process](https://www.dell.com/support/article/es/es/esbsdt1/sln306440/killer-n1535-wireless-firmware-manual-update-guide-for-ubuntu-systems?lang=en) 🤞.

## Touchpad

Check that you're using Xorg instead of Wayland (you can choose them in the login window, with the gear icon) and [install and configure fusuma](https://askubuntu.com/questions/1034624/touchpad-gestures-in-ubuntu-18-04-lts).

## Balance

### Positive stuff

- I might've spent ~3 days installing and configuring stuff. But running anything inside Vagrant with LXC support is SO FAST that I think that I've already paid it off.
- Gnome is very user friendly, specially for a developer. Built-in support for VPNs (with good DNS resolution), options to restrict background downloads in some WiFis (for phone tethering), task switching works, no matter the status of the window...
- :heart: USB ports.

### Differences

- Trackpad is pretty good and big enough. ~~I still sometimes lose the focus because I trigger clicks, though~~ I've disabled "tap to click" and I'm happy.
- Keyboard layout is slightly different, I still have to get used to it as well, specially because I'm using FXX and Fn a lot. Mac Pro butterfly keyboard felt slightly better but, as you know, it's broken. Dell one might be the second best I've ever used after that one, and hopefully it will last longer.
- In order to make "workspaces" ("virtual desktops") span both displays in Ubuntu you have to install Gnome tweaks application.

### Pending stuff

- Research about nVidia drivers.
- Gentle power down: shutting down kills applications without mercy, and Chrome and Firefox think that they crashed.
- Fingerprint reader doesn't work in Linux yet, there's no available driver.

### Missing stuff

- I need Windows for my amateurish Lightroom, Photoshop and Premiere tasks. I'll give Darktable a try.
- Gnome can't remember the applications that you were running when you logged out. I've workarounded it with "startup applications".
- Three finger gesture to display running applications. I workaround it with the simpler task switcher shortcut and the worplaces switcher shortcut, but it'll be hard to forget (although workarounds are arguably faster).
- Terminator ([still](https://bugs.launchpad.net/terminator/+bug/1301605)) doesn't support native tmux integration (like iTerm does).

## Conclusion

I've already spent more than two weeks with this setup and I'm _really_ happy. In my case, there's no single reason to stay with Apple for software development ([I'm not alone here](https://robots.thoughtbot.com/bye-apple-hello-tux)). For me, **this computer with Linux is just better, faster and cheaper**. Let's revisit this in some months!

# XPS 7590

One year later, I bought the next version of that computer, the XPS 9570 (same configuration, i7).
The only remarkable change is the camera placement, which is great, no more Carrey-sh look :-)

My goal was a dual boot system (Linux + Windows) with LUKS cypher in Linux (for security and Dropbox support).
While I had a good experience with Ubuntu, many colleagues suggested Arch Linux as a better alternative, so I decided
to give it a try (everybody told me to forget about Gentoo complexities nowadays).
[This distro comparison also helped](https://wiki.archlinux.org/index.php/Arch_compared_to_other_distributions#Gentoo/Funtoo_Linux):
- Installing Arch is based on precompiled binaries (so it's quicker than a normal source-based Gentoo installation).
- Simpler than Gentoo (I don't need/want more powerful settings).
- Rolling-based (instead of discrete as Ubuntu).

Arch Linux documentation is just gorgeous. I've written down here some notes for my future self. If you're interested,
check the references.

## Resources

- [Dell XPS 15 7590](https://wiki.archlinux.org/index.php/Dell_XPS_15_7590) > [9570](https://wiki.archlinux.org/index.php/Dell_XPS_15_9570) > [9560](https://wiki.archlinux.org/index.php/Dell_XPS_15_9560).
  - [SOLUTION: Switch Windows 10 from RAID/IDE to AHCI operation](https://triplescomputers.com/blog/uncategorized/solution-switch-windows-10-from-raidide-to-ahci-operation/).
- [Installation guide](https://wiki.archlinux.org/index.php/Installation_guide).
    - [FAQ](https://wiki.archlinux.org/index.php/Frequently_asked_questions).
    - [USB flash installation media](https://wiki.archlinux.org/index.php/USB_flash_installation_media).
    - [Dual boot with Windows](https://wiki.archlinux.org/index.php/Dual_boot_with_Windows).
- [Partitioning](https://wiki.archlinux.org/index.php/Partitioning).
- [Encrypting](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LUKS_on_a_partition).
- [Suspend and hibernate](https://wiki.archlinux.org/index.php/Power_management/Suspend_and_hibernate), [with encryption](https://wiki.archlinux.org/index.php/Dm-crypt/Swap_encryption#With_suspend-to-disk_support).
- [General recommendations](https://wiki.archlinux.org/index.php/General_recommendations).
- [List of applications](https://wiki.archlinux.org/index.php/List_of_applications).
- [SSH keys and agents](https://wiki.archlinux.org/index.php/SSH_keys).
- [pacman tips and tricks](https://wiki.archlinux.org/index.php/Pacman/Tips_and_tricks).
- [Swap](https://wiki.archlinux.org/index.php/Swap#Swap_file).

## Steps

### Base installation

1. Created a Windows Bootable pendrive with [this Microsoft Tool](https://www.microsoft.com/en-us/software-download/windows10ISO), just in case...
2. Disabled Fast Startup: Power Settings --> System Configuration --> uncheck "Turn on fast startup".
3. Reduced Windows partition to 150GB (with Windows disk management).
4. Followed "SOLUTION: Switch Windows 10 from RAID/IDE to AHCI operation" guide.
5. Additional BIOS setup (F2 at POST):
    1. Disable Secure Boot --> Secure Boot Enable (not supported by Arch Linux). Restarting Windows led to a Bitlocker recovery page, but adding the key from [their site](https://account.microsoft.com/devices/recoverykey) worked. You only need to do this once.
    2. Change SATA Operation from "RAID On" to "AHCI" so SSD can be detected.
    3. Behavior --> Fastboot --> Thorough.
6. Booted USB Arch Linux image (F12) and followed the installation guide until "Partition the disks", where
I begun [LUKS on a partition](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LUKS_on_a_partition):
      1. As the computer already has an EFI partition and I don't need encrypted root, I just created a partition with all the remaining free space for `/`:
          ```
         (parted) print devices
         (parted) select /dev/nvme0n1
         (parted) mkpart primary ext4 
         # ... accept the realignment
          ```
      2. Then, mounted boot partition at `/mnt/boot` and resumed the installation steps.
7. Installed rEFInd as boot manager. [Some peopled complained](https://twitter.com/skgsergio/status/1158735933786742784)
and suggested that [systemd-boot is even simpler](https://twitter.com/skgsergio/status/1158755069069860864) :shrug:

Additionally installed software: `wpa_supplicant`.

### Next steps

_Some of this will eventually get to my [configuration files repo](https://github.com/juanignaciosl/configuration_files)._

[Keyboard configuration](https://wiki.archlinux.org/index.php/Linux_console/Keyboard_configuration):
- Set `KEYMAP=es` at `/etc/vconsole.conf`.

- Base packages: `vim`, `git`, `gnome` (`gdb` enabled), `sudo`, [`networkmanager`](https://wiki.archlinux.org/index.php/NetworkManager), `firefox-developer-edition`, `base-devel`, `yay`, `gnome-tweaks`, `extra/ttf-dejavu`, `ruby`, `htop`, `terminator`, `bash-completion`, `powertop`.
- AUR packages: `intellij-toolbox`, `dropbox`, `spotify`, `telegram-desktop-bin`.

- `sudo` isn't installed by default (:shrug:). I followed [@sksergio suggestion to make my life a little more comfortable](https://twitter.com/skgsergio/status/1158775429328715777):

```bash
pacman -Sy # update database
pacman -S sudo
usermod -a -G wheel juanignaciosl
visudo
```

There, uncomment `%wheel ALL=(ALL) NOPASSWD: ALL`.

#### nvidia **WIP**

_WIP disclaimer: I've tried all this, no luck yet._

[Tried automatic management with bumblebee](https://wiki.archlinux.org/index.php/Dell_XPS_15_9570#Letting_bumblebee_automatically_unload_the_kernel_module).

In addition, I had to...

- Install `bbswitch`.
- Add my user to `bumblebee` group.
- Change `PMMethod=auto` to `PMMethod=bbswitch`.
- From [this](https://bbs.archlinux.org/viewtopic.php?id=185865):
  - In `/etc/mkinitcpio.conf` I added `bbswitch` to `MODULES` var: `MODULES="bbswitch"` and then `sudo mkinitcpio -p linux`.
  - In `/boot/refind_linux.conf` I added `rcutree.rcu_idle_gp_delay=1` to the boot options.
  - In `/etc/bumblebee/bumblebee.conf` under [optirun] I changed the bridge method to `Bridge=virtualgl`.
  - In `/etc/bumblebee/xorg.conf.nvidia` I set the BusID of my NVIDIA card (`lspci | grep VGA | grep NVIDIA`): `BusID "PCI:01:00:0"`.
  - Added `pcie_port_pm=off` to boot options ([source](https://www.reddit.com/r/archlinux/comments/633xdi/secondary_gpu_not_working_with_powersaving_tools/)).
  - Pending: [this](https://bbs.archlinux.org/viewtopic.php?id=238389).

This is the error that I see:

```bash
[juanignaciosl@127 ~]$ optirun glxgears
[  436.562997] [ERROR]Cannot access secondary GPU - error: Could not enable discrete graphics card

[  436.563124] [ERROR]Aborting because fallback start is disabled.
```

```bash
[juanignaciosl@127 ~]$ journalctl | tail -n 10
Aug 07 10:59:07 127.0.0.1localhost bumblebeed[752]: Could not enable discrete graphics card
Aug 07 10:59:07 127.0.0.1localhost kernel: bbswitch: enabling discrete graphics
Aug 07 10:59:07 127.0.0.1localhost kernel: pci 0000:01:00.0: Refused to change power state, currently in D3
Aug 07 10:59:07 127.0.0.1localhost kernel: pci 0000:01:00.0: Refused to change power state, currently in D3
```

### TODO checklist

- Will Windows keep asking for Bitlocker key?
- Swap and hibernation.
- Working dedicated graphic card on/off.

## Restoring an overwritten boot

After some Windows updates you can find yourself stuck into Windows :scream: . Just reinstalling rEFInd worked for me.
Boot the USB stick that you used for installing.

Previous notes:
- If you don't remember which are your `/` or `/boot` partition you can check it at `parted` with `print all`.
- If you need network connection, after chrooting you can use your already existing configuration:
```bash
$ ip link set <link> up
$ wpa_supplicant -B -c /etc/wpa_supplicant/<your_ssid>.conf
$ dhclient <link>
```

Here are my steps:

```bash
$ loadkeys es
$ cryptsetup open /dev/nvme0n1p7 cryptroot
$ mount /dev/mapper/cryptroot /mnt
$ mount /dev/nvme0n1p1 /mnt/boot
$ arch-chroot /mnt
$ refind-install
```

## Everything goes wrong

2020 couldn't end without a crashing system.

I run `sudo pacman -Syu` and `yay -Syu` in a weekly basis to keep the system up to date. It had caused no problem so far.
Today, upgrading crashed (the last line that I saw was an update of systemd).
The computer wouldn't boot because of a corrupted kernel.
Starting with a USB and chrooting lead to a non-working pacman that wouldn't allow to reinstall the kernel.

I didn't keep logs, [you can see me crying here](https://twitter.com/juanignaciosl/status/1309747730999316482), including
[the initial kernel panic](https://twitter.com/juanignaciosl/status/1309747730999316482/photo/1). While I didn't check
it, it seemed to be a corrupted image. Anyway, having a broken `pacman` is even worse. After logging with a USB recovery
image, I chrooted and tried to run `mkinitcpio` to rebuild the kernel, but it failed with a lot of `Missing module` errors.
If I tried to run `pacman -S linux`, I got `/usr/lib/libcrypto.so.1.1 file too short`. That file had zero-length, it
was corrupted during the broken installation. There's 
[a guide about what to do if pacman crashes during an upgrade](https://wiki.archlinux.org/index.php/pacman#Pacman_crashes_during_an_upgrade),
but it would keep returning the `file too short` error. It looks like there's a hard dependency on `openssl` that I
wasn't able to workaround. I tried moving the symlinks at `/usr/lib`, but it wouldn't work.
BTW, for `pacman` to run I had to delete `/var/lib/pacman/db.lck` file.
There are [statically-linked pacman packages](https://aur.archlinux.org/packages/pacman-static/). I downloaded it, but
it wasn't a solution either, I got plenty of `<filename> already exists in filesystem`.
At that moment it became obvious that any workaround would require a lot of manual fixing (file deletion and so on),
so I chose to reinstall. That wasn't as straightforward as I would've wanted, because `pacstrap` doesn't override, and keeps
some of the `pacman` checks, so I had to manually rename the base folders.
 
BTW, I also got a `invalid value Path` error related to a hook, but the cause was using the USB image that I used for
the installation one year ago, which was outdated. A new image addressed that.

### Outcome

#### Other useful links

- [iwd](https://wiki.archlinux.org/index.php/Iwd), a command line assistant for the Wi-Fi.
- [Arch Linux mkinitcpio: Possibly missing firmware for module.md](https://gist.github.com/imrvelj/c65cd5ca7f5505a65e59204f5a3f7a6d).
- [l-page of the Arch archive](https://archive.archlinux.org/packages/l/) for downloading old versions.


#### The good parts

- `/home` wasn't lost, so all the data and most of the configuration was kept (creating the users with 
  `useradd -d <oldpath> username` worked like a charm).
- I was (almost) sure of not losing anything, even in an encrypted hard drive, mostly because of the support of the
  great documentation and the nice tooling.

#### The bad part

- I lost one full day. Maybe I shouldn't have been so stubborn about bringing `pacman` back to live and reinstalled earlier.
- Being able to install from the boot environment with `--sysroot` is nice, but it'd be great if allowed fixing a broken
  pacman.

#### The actions that worked

1. I booted with a USB recovery image, and mounted root and boot (see "Restoring an overwritten boot").
2. I renamed `bin`, `etc`, `lib`, `lib64`, `sbin`, `usr` and `var` to `...backup` so it wasn't used by `pacman` or
   `pacstrap`. I should've renamed `proc` as well.
3. [Changed `mkinitcpio.conf` file to support LUKS encryption](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#Configuring_mkinitcpio).
4. As during the process I had wiped the pacman cache (because it might've been corrupted) I **manually downloaded
   `5.7.12-arch1-1` `linux` and `linux-headers` packages for [downgrading the kernel](https://wiki.archlinux.org/index.php/downgrading_packages#Downgrading_the_kernel)**
   and crossed my fingers very hard, because my hypothesis at that moment was that the kernel was the root cause of the crash.
4. Resumed the [installation guide from the `pacstrap` step](https://wiki.archlinux.org/index.php/installation_guide#Install_essential_packages).
5. Created my user with `-d` to keep the existing home directory.
6. Avoided `pacman` to update `linux` or `linux-headers` by adding `IgnorePkg` entries to `/etc/pacman.conf`.
7. Reinstalled most of my software with [my install script](https://github.com/juanignaciosl/configuration_files/blob/master/install.sh).
8. Added `linux` and `linux-headers` to `/etc/pacman.conf`, at `IgnorePkg` sections, so they're not always updated.

IIRC, back in Gentoo times, upgrading the kernel kept old versions so rebooting with a working one was easier. I have to
bring that back to my current setup.

#### One more thing

Arch has a `linux-lts` package for installing a long-term support version of the Linux kernel. That's a good way to
keep both the most up-to-date kernel and a stable version.

## New summary when things go wrong

1. Boot with a USB recovery image.
2. Mount drives:
```bash
$ cryptsetup open /dev/nvme0n1p7 cryptroot
$ mount /dev/mapper/cryptroot /mnt
$ mount /dev/nvme0n1p1 /mnt/boot
```
3. Resume [installation](https://wiki.archlinux.org/index.php/Installation_guide) from `pacstrap`. Don't forget running
`locale-gen` and installing `dhcpcd`.
4. `useradd -m USERNAME -d EXISTING_HOME`, `passwd USERNAME`.
