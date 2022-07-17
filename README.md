# live-toolkit
Shell scripts that aid in customizing Ubuntu LiveCD's. Only tested with Ubuntu 20.04, SHA256 `f92f7dca5bb6690e1af`. Based on https://help.ubuntu.com/community/LiveCDCustomization

# Usage
`./mount file.iso` - Mounts a Ubuntu LiveCD installer ISO, extracts its data, and unsquashes `filesystem.squashfs`. Safe to call multiple times.

`./chroot` - Mounts required directories and chroots into the extracted squashfs using /bin/bash. `exit` must be called within the chroot to exit safely, otherwise you'll need to reboot your system to fix `/dev`.

`./buildiso original.iso custom.iso` - Builds the ISO from the extracted squashfs image you chrooted into. original.iso is the path to the original ISO you opened and custom.iso is the name for the generated ISO.

`./clean` - Cleans and deletes all files used for building the ISO. DO NOT CALL THIS IF CHROOT FAILED.

`./bakesquash original.iso filesystem.squashfs` - "Bakes" a filesystem.squashfs file from some other custom ISO (say a USB) into a known good LiveCD ISO. Very useful if you need to recover from a single USB image. 

`./tools` - Misc bash functions for scripts. 
