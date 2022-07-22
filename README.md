# live-toolkit
Shell scripts that aid in customizing Ubuntu LiveCD's. Only tested with Ubuntu 20.04, SHA256 `f92f7dca5bb6690e1af`. Based on https://help.ubuntu.com/community/LiveCDCustomization

# Usage
`./mount file.iso` - Mounts a Ubuntu LiveCD installer ISO, extracts its data, and unsquashes `filesystem.squashfs`. Safe to call multiple times.

`./chroot` - Mounts required directories and chroots into the extracted squashfs using /bin/bash. `exit` must be called within the chroot to exit safely, otherwise you'll need to reboot your system to fix `/dev`.

`./buildiso original.iso custom.iso` - Builds the ISO from the extracted squashfs image you chrooted into. original.iso is the path to the original ISO you opened and custom.iso is the name for the generated ISO.

`./clean` - Cleans and deletes all files used for building the ISO. DO NOT CALL THIS IF CHROOT FAILED.

`./bakesquash original.iso filesystem.squashfs` - "Bakes" a filesystem.squashfs file from some other custom ISO (say a USB) into a known good LiveCD ISO. Very useful if you need to recover from a single USB image. 

`./tools` - Misc bash functions for scripts. 

# Other Info
If by chance the system is unbootable or you are unable to create the iso, try to crank down the `iso-level` in `buildiso` to 2 instead of 3. The option reduces the max squashfs size to 4GB, but it building it this way is more compatible with older systems. 

I heavily advise against ripping an ISO from a LiveCD USB solely due to the hybrid nature of the partition tables. If you trim the image (which, in all likelyhood of 32GB+ USBs, you are) you break the partition tables somewhat, and it'll just not be nice. Just copy down the `filesystem.squashfs` file and use the `bakesquash` to combine it with a known good image. 

After using `mount` or `bakesquash`, you can just not touch the system. You can turn it off, reboot, etc, and it'll be completely fine and you can `chroot` and `buildiso` where you left off. Same with `chroot`; You can exit and reenter as many times as you want. 

The Ubuntu LiveCD I use uses the same amount of RAM regardless of how big the ISO is: 2GB or 48GB, it doesn't matter. For some reason, reading off the USB is ULTRA-FAST. It may be because everything is written to memory directly so less instructions have to be run, but the read speed is probably as good as a SSD. 

Writes are obviously done to RAM too. Everything. If you create a 1GB file, it will use 1GB or RAM. If you delete it, the RAM is freed immediately. Same with packages. This means you are able to use the 2G/S (since RAM is like 10x faster than anything else), so in theory writing is actually WAY faster than a SSD as well. The USB isn't harmed doing this, so win-win. 

No persistence just yet. [This](https://help.ubuntu.com/community/LiveCD/Persistence) might be a very handy guide in the future for creating a script just for that. It'd be great if persistence is optional, as writing to RAM is WAYYYYY faster so persistence might be good for just the `home` directory or something. 

These scripts can easily work on other LiveCDs with small modifications (most likely just the names of files updated after squashfs is built). Make sure you know what you are doing. 
