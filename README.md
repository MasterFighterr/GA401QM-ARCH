# GA401QM-ARCH
## Installing Arch on a G14 3060/5900HS

This guide is for getting a vanilla Arch install on the G14. The linux kernel has been adapted very well to the GA401 series, so if you have another variant other than the 3060, you SHOULD be able to benefit from the information in this article even if you do not have a 5000 series CPU or a 3060. 

If you are dualbooting windows (or duel-booting as I call it as I run the windows install from a VM) INSTALL WINDOWS FIRST. I recommend Windows 10 AME for a few reasons:
It is debloated (no windows update breaking grub!)
No spooky BS (telemetry is gone!)

Since this is a guide for an Arch install and not a Windows install, you are on your own with that one. You pretty much just need to install the drivers. Installing the nvidia drivers will be a PITS, installing the AMD software will be a bigger PITS, don't install Geforce Experience because it will ruin the entire reason of having AME (telemetry). I will update with the solution to a lack of Nvidia Control panel as you cannot install standard drivers, therefore you are unable to install the regular control panel (atleast you THINK, I have a solution for this however!)

## Disclaimer________________________________________
I am not responsible for you breaking your system. This guide assumes you already have a machine that has fastboot and secureboot disabled, and if you are dualbooting, a windows installation already set up. I am not going to tell you some of the "best practices" nonsense that the arch wiki tells you because those things are assuming that you don't update your install-disk frequently. Use a fresh iso for God's sake.

## Pre-Install_______________________________________
You can set it up however you like, however this is how I have it set up with duel-booting
> /dev/nvme0n1p1 SWAP 16 GB
> /dev/nvme0n1p2 BOOT 1GB (I will briefly tell you how to do this on windows)
> /dev/nvme0n1p3 MSI partition or whatever
> /dev/nvme0n1p4 Windows Partition
> /dev/nvme0n1p5 /
> /dev/nvme0n1p6 /Home

Windows is going to make the boot partition too small to be able to do anything useful beyond booting its own selfish OS. Download a program called AOMEI Partition Assistant and extend the boot partition to 1GB. Is that overkill? Absolutely, but data on SSDs are like $.2 a GB right now, I think you will manage.

## Arch Install_______________________________________

Boot up your Arch disk and choose whatever option suits your needs. Before you actually press enter though, keep in mind that Nvidia decided to pull an EA and force the 30-series GPUs to only be compatible with the proprietary drivers, not that nouveau is very performance-friendly to begin with. You'll need to add this line to the boot parameters:

> modprobe.blacklist=nouveau

Once it boots in (You might have to do this twice because it seems the arch install disk tends to kernel panic with this particular series of laptops), go ahead and enter iwctl. You can connect to your wifi using this template below

> station wlan0 connect "NETWORK_SSID_HERE"

> password:ENTER_PASSWORD_HERE

After you've connected, ping google.com so as to give them a middle finger and announce that you have begun your life journey as a fellow tinfoil-hat-wearer. After you have finished growing a few more inches on your neckbeard, go ahead and set your system clock

> timedatectl set-ntp true

Then we will use cgdisk to partiton our drive.

> cgdisk /dev/nvme0n1

> /dev/nvme0n1p1 Linux Swap

> /dev/nvme0n1p2 (no changes)

> /dev/nvme0n1p5 30 GB

> /dev/nvme0n1p6 rest of your hard drive space

Write changes to the disk and exit cgdisk

Run these commands:

`mkswap /dev/nvme0n1p1
mkfs.btrfs /dev/nvme0n1p5
mkfs.btrfs /dev/nvme0n1p6`
