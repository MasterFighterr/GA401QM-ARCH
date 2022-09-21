# GA401QM-ARCH
## Installing Arch on a G14 3060/5900HS

This guide is for getting a vanilla Arch install on the G14. The linux kernel has been adapted very well to the GA401 series, so if you have another variant other than the 3060, you SHOULD be able to benefit from the information in this article even if you do not have a 5000 series CPU or a 3060. 

## Pre-Install
You can set it up however you like, here is my scheme. I like to use Btrfs particularly due to the
subvolume features

`/`

`/dev/nvme0n1p2 BOOT 1GB` (I will briefly tell you how to do this on windows)

`/dev/nvme0n1p3 MSI partition or whatever`

`/dev/nvme0n1p4 Windows Partition`

`/dev/nvme0n1p5 /`

`/dev/nvme0n1p6 /Home`


## Arch Install

`# pacstrap /mnt base linux linux-firmware sudo vim networkmanager pacman-contrib`

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

`# grub-install --target=x86_64-efi --efi-directory=/efi/boot/grub --bootloader-id=GRUB`

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

Go ahead and make your user and add yourself to the sudoers file, and uncomment the first wheel option. Add yourself to the wheel group.

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

Let's get asus notify rolling

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
