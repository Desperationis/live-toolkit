# live-toolkit
Shell scripts that aid in customizing ANY LiveCD. Tested with Ubuntu and Pop OS (both 20.04 and 22.04 for each) and various others debian-based distros. Method is based on https://help.ubuntu.com/community/LiveCDCustomization

# Requirements
`xorriso` and `squashfs-tools` needs to be installed in order to unpack and rebuild the ISO. On debian this can be installed with `sudo apt-get install xorriso squashfs-tools`.

# Usage
`./preview file.iso` - Simply mounts a LiveCD so you can inspect its contents in mnt/

`./mount file.iso` - Mounts a LiveCD ISO, extracts its data, and unsquashes `filesystem.squashfs`. Safe to call multiple times.

`./chroot` - Mounts required directories and chroots into the extracted squashfs using /bin/bash. `exit` must be called within the chroot to exit safely, otherwise you'll need to reboot your system to fix `/dev`.

`./buildiso original.iso custom.iso` - Builds the ISO from the extracted squashfs image you chrooted into. original.iso is the path to the original ISO you opened and custom.iso is the name for the generated ISO.

`./clean` - Cleans and deletes all files used for building the ISO. DO NOT CALL THIS IF CHROOT FAILED.

`./bakesquash original.iso filesystem.squashfs` - "Bakes" a filesystem.squashfs file from some other custom ISO (say a USB) into a known good LiveCD ISO. Very useful if you need to recover from a single USB image. 

`./tools` - Misc bash functions for scripts. 

`./config` - This is the file that contains the LiveCD-specific directories of some things. You can configure this to fit other LiveCDs with ease. 

# Other Info
### ISO won't boot up
If by chance the system is unbootable or you are unable to create the iso, try to crank down the `iso-level` in `buildiso` to 2 instead of 3. The option reduces the max squashfs size to 4GB, but it building it this way is more compatible with older systems. 

### Lost my ISO. How do I recover it from a USB?
I heavily advise against ripping an ISO from a LiveCD USB solely due to the hybrid nature of the partition tables. If you trim the image (which, in all likelyhood of 32GB+ USBs, you are) you break the partition tables somewhat, and it'll just not be nice time. It'd be much easier to copy down the `filesystem.squashfs` file and use the `bakesquash` to combine it with a known good image. 

### Do I need to modify the ISO in one go without rebooting?
After using `mount` or `bakesquash`, you can just not touch the system. You can turn it off, reboot, etc, and it'll be completely fine and you can `chroot` and `buildiso` where you left off. Same with `chroot`; You can exit and reenter as many times as you want. 

### How fast is the I/O speed?
The Ubuntu LiveCD I use uses the same amount of RAM regardless of how big the ISO is: 2GB or 48GB, it doesn't matter. For some reason, reading off the USB is ULTRA-FAST. It may be because everything is written to memory directly so less instructions have to be run, but the read speed is probably as good as a SSD. 

Writes are obviously done to RAM too. Everything. If you create a 1GB file, it will use 1GB or RAM. If you delete it, the RAM is freed immediately. Same with packages. This means you are able to use the 2G/S (since RAM is like 10x faster than anything else), so in theory writing is actually WAY faster than a SSD as well. The USB isn't harmed doing this, so win-win. 

### Persistence?
No persistence just yet. [This](https://help.ubuntu.com/community/LiveCD/Persistence) might be a very handy guide in the future for creating a script just for that. It'd be great if persistence is optional, as writing to RAM is WAYYYYY faster so persistence might be good for just the `home` directory or something. 

### Other LiveCD's
These scripts can easily work on other LiveCDs with small modifications to `config` (most likely just the names of files updated after squashfs is built). Make sure you know what you are doing. 

### How do I change the default username and host?
This heavily depends on the exact distro. At the moment, the usual way of doing it is to [modify](https://help.ubuntu.com/community/LiveCDCustomization) `/usr/share/initramfs-tools/scripts/casper` in the squashfs and replace all instances of `USERNAME` and `HOST` to your liking. Another way of doing it is to unpack the original initrd and make modifications there, but it is [sometimes difficult](https://askubuntu.com/questions/1229964/how-to-extract-initrd-from-ubuntu-20-04-live-server-amd64-iso) depending on the distro. Then, you'll need to recompile `initrd` in `/casper` for the changes to appear. Recompiling is the hardest part, as some distros (like Ubuntu) use some weird linux magic to make recompiling initramfs extremely difficult. 

It doesn't get better. `initrd` can be secretly a `gz` or `lz` file, or it might not be compressed at all and just be a `cpio` file instead. In the case of Ubuntu, it contains multiple different "layers" (they are just `cat` together). You can inspect the specific layout `initrd` by using a file-inspection tool like [binwalk](https://github.com/ReFirmLabs/binwalk)



