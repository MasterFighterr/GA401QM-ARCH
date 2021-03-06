# GA401QM-ARCH
## Installing Arch on a G14 3060/5900HS

This guide is for getting a vanilla Arch install on the G14. The linux kernel has been adapted very well to the GA401 series, so if you have another variant other than the 3060, you SHOULD be able to benefit from the information in this article even if you do not have a 5000 series CPU or a 3060. 

If you are dualbooting windows (or duel-booting as I call it as I run the windows install from a VM) INSTALL WINDOWS FIRST. I recommend Windows 10 AME for a few reasons:
It is debloated (no windows update breaking grub!)
No spooky BS (telemetry is gone!)

Since this is a guide for an Arch install and not a Windows install, you are on your own with that one. You pretty much just need to install the drivers. Installing the nvidia drivers will be a PITS, installing the AMD software will be a bigger PITS, don't install Geforce Experience because it will ruin the entire reason of having AME (telemetry). I will update with the solution to a lack of Nvidia Control panel as you cannot install standard drivers, therefore you are unable to install the regular control panel (atleast you THINK, I have a solution for this however!)

## Disclaimer
I am not responsible for you breaking your system. This guide assumes you already have a machine that has fastboot and secureboot disabled, and if you are dualbooting, a windows installation already set up. I am not going to tell you some of the "best practices" nonsense that the arch wiki tells you because those things are assuming that you don't update your install-disk frequently. Use a fresh iso for God's sake.

## Pre-Install
You can set it up however you like, however this is how I have it set up with duel-booting

`/dev/nvme0n1p1 SWAP 16 GB`

`/dev/nvme0n1p2 BOOT 1GB` (I will briefly tell you how to do this on windows)

`/dev/nvme0n1p3 MSI partition or whatever`

`/dev/nvme0n1p4 Windows Partition`

`/dev/nvme0n1p5 /`

`/dev/nvme0n1p6 /Home`

Windows is going to make the boot partition too small to be able to do anything useful beyond booting its own selfish OS. Download a program called AOMEI Partition Assistant and extend the boot partition to 1GB. Is that overkill? Absolutely, but data on SSDs are like $.2 a GB right now, I think you will manage.

## Arch Install
Boot up your Arch disk and choose whatever option suits your needs. Before you actually press enter though, keep in mind that Nvidia decided to pull an EA and force the 30-series GPUs to only be compatible with the proprietary drivers, not that nouveau is very performance-friendly to begin with. You'll need to add this line to the boot parameters:

`modprobe.blacklist=nouveau`

Once it boots in (You might have to do this twice because it seems the arch install disk tends to kernel panic with this particular series of laptops), go ahead and enter iwctl. You can connect to your wifi using this template below

`# station wlan0 connect "NETWORK_SSID_HERE"`

`# password:ENTER_PASSWORD_HERE`

After you've connected, ping google.com so as to give them a middle finger and announce that you have begun your life journey as a fellow tinfoil-hat-wearer. After you have finished growing a few more inches on your neckbeard, go ahead and set your system clock

`timedatectl set-ntp true`

Then we will use cgdisk to partiton our drive.

`cgdisk /dev/nvme0n1`

`/dev/nvme0n1p1 Linux Swap`

`/dev/nvme0n1p2 (no changes)`

`/dev/nvme0n1p5 50 GB` (30 if stock 1tb drive, this is assuming you have upgraded to 2tb)???

45

`timedatectl set-ntp true`

46

???

47

Then we will use cgdisk to partiton our drive.

48

???

49

`cgdisk /dev/nvme0n1`

50

???

51



`/dev/nvme0n1p6 rest of your hard drive space`

Write changes to the disk and exit cgdisk

Run these commands:

`# mkswap /dev/nvme0n1p1`

`# mkfs.btrfs /dev/nvme0n1p5`

`# mkfs.btrfs /dev/nvme0n1p6`

`# mkfs.fat -F 32 /dev/nvme0n1p2` (ONLY DO THIS IF YOU DIDN'T INSTALL WINDOWS)

`# mount /dev/nvme0n1p5 /mnt`

`# mount --mkdir /dev/nvme0n1p6 /mnt/home`

`# mount --mkdir /dev/nvme0n1p2 /mnt/boot`

`# swapon /dev/nvme0n1p1`

Now we will install Arch. Consider yourself lucky to live in the days of soystemd, as back in the sysV days you did NOT have pacstrap. This is why Arch is a just-werks distro, you toesucking neckbeards on r/unixporn and r/linuxmasterrace can brag and gloat all you want but pacstrap literally does everything for you man.

`# pacstrap /mnt base linux linux-firmware sudo vim networkmanager pacman-contrib`

WOAH AMAZING IT INSTALLED ASDJFLKASDFJKLASDFLAKJSDFKLSJLAKSDJFLASDKJFLKSJDLFJSDFSLDKJF

`# genfstab -U /mnt >> /mnt/etc/fstab`

`# arch-chroot /mnt`

`# ln -sf /usr/share/zoneinfo/Region/City /etc/localtime`

`# hwclock --systohc`

Edit /etc/locale.gen and uncomment the proper UTF-8 line. I'm a burgercan so I uncommented en_US.UTF-8 and ran

`# locale-gen`

`# vim /etc/locale.conf`

you know you should be using vim, nano is dirty dirty :)

`LANG=en_US.UTF-8`

`# vim /etc/hostname`

Write whatever you want your machine to be named

set the root password

`# passwd`

Choose your bootloader, just know that if it is anything besides GRUB the community will look down on you and you will be doomed to never recieve support from anyone in the Arch community ever again (like they'll do anything besides tell you to read the wiki anyways lmao)

`# pacman -S grub efibootmgr os-prober`

`# grub-install --target=x86_64-efi --efi-directory=esp --bootloader-id=GRUB`

`# vim /etc/default/grub`

uncomment this line

`# GRUB_DISABLE_OS_PROBER=false`

`# grub-mkconfig -o /boot/grub/grub.cfg`

reboot, make sure to add the modprobe.blacklist=nouveau parameter again upon rebot

Congratulations! You are officially an Arch user! If you are not a newbie (and if you are, git gud first), then you probably don't really need what I'm about to tell you up next, but even if you are a seasoned (with Slap Ya Mama) neckbeard with atleast 18 inches of hair and at minimum 2 layers of tinfoil on your head, you could still benefit from this information as it will add QOL features to your Arch G14 experience.

## Getting the ball rolling
First things first, let's add the g14 supposi- I meeean repository to pacman.conf

`# vim /etc/pacman.conf`

add this at the end

`[g14]`

`SigLevel = DatabaseNever Optional TrustAll`

`Server = https://arch.asus-linux.org`

while we are here, uncomment this

`[multilib]`

and the lines below it

then run

`# sudo pacman -Syu`

`# sudo pacman -S linux-g14 linux-g14-headers base-devel`

After this, we're going to reboot into the new kernel, but let's save ourselves some effort by doing this

`# sudo vim /etc/default/grub`

disable submenus and change boot option to be 2. Save and write

`# sudo grub-mkconfig /boot/grub/grub.cfg`

reboot

Hopefully the selected option will be the g14 kernel, if not, select the kernel, change it in grubs config and run that command again, no need to reboot.

Go ahead and make your user and add yourself to the sudoers file, and uncomment the first wheel option. Add yourself to the wheel group

We need to blacklist nouveau, do this

`# sudo vim /etc/modprobe.d/blacklist.conf`

`blacklist nouveau'

then this

`# sudo vim /etc/modprobe.d/nvidia.conf

`options nvidia_drm modeset=1`

`options nvidia NCreg_DynamicPowerManagement=0x02`

then this

`# sudo vim /etc/udev/rules.d/80-nvidia-pm.rules`

`# Enable runtime PM for NVIDIA VGA/3D controller devices on driver bind`

`ACTION=="bind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030000", TEST=="power/control", ATTR{power/control}="auto"`

`ACTION=="bind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030200", TEST=="power/control", ATTR{power/control}="auto"`

`# Disable runtime PM for NVIDIA VGA/3D controller devices on driver unbind`

`ACTION=="unbind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030000", TEST=="power/control", ATTR{power/control}="on"`

`ACTION=="unbind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030200", TEST=="power/control", ATTR{power/control}="on"`

then after that, enable nvidia-persistenced

`sudo systemctl enable --now nvidia-persistenced`

Then you're done with that, go ahead and reboot to be safe

Now we are going to install asusctl and supergfxctl

`# pacman -S asusctl supergfxctl`

`# systemctl enable --now power-profiles-daemon.service`

`# systemctl enable --now supergfxd.service`

Now switch your supergfx mode to integrated

`# supergfxctl -m integrated`

logout and log back in

install the nvidia drivers

`# pacman -S nvidia-dkms nvidia-utils lib32-nvidia-utils`

install nvidia prime

`# pacman -S nvidia-prime`

switch back to hybrid

`# supergfxctl -m hybrid`

logout and log back in

install the mesa drivers. OK now listen to me here, do NOT install anything amd related besides these, I promise you that this will save you hours upon hours of ass pain. If you install anything besides the regular mesa drivers and the xf86 driver, proton and wine will straight up flat out refuse to run, don't ask me why, I nuked my steam folder (thankfully not my 1.3 tb of games though because I ain't no basic bitch :D) and found this out the hard way so that YOU didn't have to, future me or whomever you may be! Also, the xf86 driver may seem unnecessary but if you want to run xorg properly at 60 fps, you absolutely need it, believe me.

`# pacman -S mesa mesa-demos xf86-video-amdgpu`

Now you pretty much have reached the point where you want to be before you install your desktop environment or window manager. I have a couple more quality of life features for you including getting the microphone button working and profiles + lighting for asusctl hotkeys.

bind fn+f4 to asusctl led-mode -n

bind fn+f5 to asusctl profile -n

fix the microphone mute button 

`sudo find /sys -name modalias | xargs grep -i 0b05`

look for a line that goes along these lines

`.../input/input25/modalias:input:b0003v0B05p19B6e0110-e0...`

Copy the part after input: before the first -e. So basically b0003v0B05p19B6. 

`# vim /etc/udev/hwdb.d/90-nkey.hwdb`

and in vim, write this out

`/etc/udev/hwdb.d/90-nkey.hwdb`

`evdev:input:b0003v0B05p19B6*`

` KEYBOARD_KEY_ff31007c=f20 # x11 mic-mute, space in start is important in this line`

write and exit

`# sudo systemd-hwdb update`

`# sudo udevadm trigger`

Your mic button should now be working.

Let's get asus notify rolling so that you know when you change your sick ass ledz and profiles :DDDDDDDDDDDDDDDDDDDDDDDDDDDD

`# systemctl --user enable asus-notify`

`# systemctl --user start asus-notify`

Let's add more threads to our makepkg,conf

`# sudo vim /etc/makepkg.conf`

uncomment the MAKEFLAGS line and add 8 threads

`MAKEFLAGS="-j8"`

Hell yeah baby, you're on a roll. You're pretty much good to go. Install whatever bloated ass DE or minimal ass WM you want. Keep in mind that the more minimal you get, the more features you will have to install yourself as well as setup like your hotkeys. If you're a cool kid you'll stick to unbloated modules like pipewire! Have fun!

For a minimal KDE Plasma install:

`# sudo pacman -S pipewire wireplumber pipewire-pulse pipewire-alsa pipewire-jack`

`# sudo pacman -S plasma-desktop dolphin dolphin-plugins ark konsole plasma-nm plasma-pa kdeplasma-addons kde-gtk-config powerdevil sddm sddm-kcm bluedevil kscreen kinfocenter firefox partitionmanager plasma-wayland-session egl-wayland spectacle`

some extras:

`# sudo pacman -S latte-dock obs-studio steam steam-native-runtime grub-customizer qbittorrent`

After that, install the ttf-liberation font from before for steam

Install yay

`# cd /opt`

`# sudo git clone https://aur.archlinux.org/yay-git.git`

`# sudo chown -R user:user ./yay-git`

`# cd yay-git`

`makepkg -si`

`yay -S minecraft-launcher jamesdsp ryzencontroller mangohud goverlay openrgb spotify`
