# GA401QM-ARCH
Installing Arch on a G14 3060/5900HS

This guide is for getting a vanilla Arch install on the G14. The linux kernel has been adapted very well to the GA401 series, so if you have another variant other than the 3060, you SHOULD be able to benefit from the information in this article even if you do not have a 5000 series CPU or a 3060. 

If you are dualbooting windows (or duel-booting as I call it as I run the windows install from a VM) INSTALL WINDOWS FIRST. I recommend Windows 10 AME for a few reasons:
It is debloated (no windows update breaking grub!)
No spooky BS (telemetry is gone!)

Since this is a guide for an Arch install and not a Windows install, you are on your own with that one. You pretty much just need to install the drivers. Installing the nvidia drivers will be a PITS, installing the AMD software will be a bigger PITS, don't install Geforce Experience because it will ruin the entire reason of having AME (telemetry). I will update with the solution to a lack of Nvidia Control panel as you cannot install standard drivers, therefore you are unable to install the regular control panel (atleast you THINK, I have a solution for this however!)

Disclaimer________________________________________
I am not responsible for you breaking your system. This guide assumes you already have a machine that has fastboot and secureboot disabled, and if you are dualbooting, a windows installation already set up. I am not going to tell you some of the "best practices" nonsense that the arch wiki tells you because those things are assuming that you don't update your install-disk frequently. Use a fresh iso for God's sake.

Pre-Install_______________________________________
You can set it up however you like, however this is how I have it set up with duel-booting
/dev/nvme0n1p1 SWAP 16 GB
/dev/nvme0n1p2 BOOT 1GB (I will briefly tell you how to do this on windows)
/dev/nvme0n1p3 MSI partition or whatever
/dev/nvme0n1p4 Windows Partition
/dev/nvme0n1p5 /
/dev/nvme0n1p6 /Home

Windows is going to make

Arch Install_______________________________________
