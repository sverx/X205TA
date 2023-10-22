The ASUS X205TA needs a 32 bit UEFI bootloader even if its processor and the operating system you are installing are 64 bit.

This is how to install a 32 bit bootloader on your eMMC after you've installed the Linux distro in case the installation failed to install it correctly.

First, *reboot into your live environment again*, so you're in a 'clean' environment.

Then, as root, issue the following commands:

```
sudo apt-get update
sudo apt-get install grub-efi-ia32 grub-efi-ia32-bin
```

then create two directories to be used as mountpoints:

```
mkdir /mnt/efi
mkdir /mnt/root
```

Now find the block device partition names where the root of your linux installation is located using `lsblk` or `blkid` (or tools such as GParted), then find also the name of the UEFI partition *note those partition names down*

Then issue the following commands (note the command needs you to use the correct destination block device partitions instead of *mmcblkXpY* and *mmcblkXpZ*):

```
# mount the rootfs partition (change X and Y) on /mnt/root
sudo mount /dev/mmcblkXpY /mnt/root

# mount the efi partition (change X and Z) on /mnt/efi
sudo mount /dev/mmcblkXpZ /mnt/efi
```

Finally install the bootloader:

```
grub-install --recheck --root-directory=/mnt/root --efi-directory=/mnt/efi
```

When completed, turn off the computer and remove the CD/DVD/USB unit with the live environment.

Turn the PC on again, it should boot correctly now!

original source: https://askubuntu.com/questions/1040519/installing-32bit-bootloader-on-64bit-ubuntu-solved

