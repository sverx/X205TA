The system needs a 32 bit UEFI bootloader even if the processor and the operating system is 64 bit.

This is how to install a 32 bit bootloader on your eMMC after you've installed the Linux distro:

Find the right block device partition where the root of your linux installation is located using lsblk or blkid.

Then issue the following commands (note the first command needs you to use the correct destination block device partition):

```
# mount the rootfs partition (change X and Y) on /mnt
sudo mount /dev/mmcblkXpY /mnt
cd /mnt
sudo mount --bind /proc proc
sudo mount --bind /sys sys
sudo mount --bind /dev dev
sudo mount --bind /dev/pts dev/pts
sudo mount --bind /run run
sudo chroot .

# you are now chrooted into the linux installation that is on the eMMC
# firstly, attempt to mount all filesystems listed in /etc/fstab, because the efi partition should be mounted
mount -a

# probably not necessary, but to be sure the EFI variables are properly exposed to userspace run:
modprobe efivarfs
mount -t efivarfs efivarfs /sys/firmware/efi/efivars

# this is the main command: it installs 32bit efi grub to the efi partition
grub-install --target=i386-efi --bootloader-id=Linux --efi-directory=/boot/efi --recheck

# (this command will fail if the necessary grub libraries are not installed, they should be in /usr/lib/grub/i386-efi/)
# (use `apt-get install grub-efi-ia32 grub-efi-ia32-bin` to install the missing libraries in case)
# (some distros use grub2-install instead of grub-install)

# generate grub.cfg
grub-mkconfig -o /boot/grub/grub.cfg
# (some distros use grub2-mkconfig)
# (likewise, on some distros you should install grub.cfg to /boot/grub2/ instead of /boot/grub/ – check which directory already exists)

# exit the chroot environment
exit

# reboot the laptop
sudo reboot
```

original source: https://ubuntuforums.org/showthread.php?t=2379657&p=13719125#post13719125

original source: https://askubuntu.com/questions/1040519/installing-32bit-bootloader-on-64bit-ubuntu-solved

