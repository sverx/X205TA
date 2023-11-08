How to prepare your Linux Distro USB key to properly boot on the Asus X205TA (and many other UEFI32 devices)

The Asus X205TA doesn't seem to really like booting from ISO9660 (or hybrid) partitions and filesystems... or at least I rarely managed to boot from such, so many common tools to prepare bootable USB keys won't work properly. So we'll have to prepare the boot USB media ourselves.

Note: you should do this *only if* the suggested way to prepare the Live (or install) media for your Distro fails miserabily. And you might just need the `bootia32.efi` file into the `/EFI/boot` folder, which you may be able to add yourself. Or sometimes the file is there but the partitioning scheme on the USB key is not MBR (Master Boot Record). Make sure it's MBR and try creating the media again.

If still fails, try this.

First, make sure the partitioning scheme on the USB key is MBR and create a *single* _bootable_ FAT32 partition. The partition doesn't need to use the whole space, but it's just easier. The partitioning scheme on the USB key should be MBR, use a tool such as GParted if you are unsure. Make sure the partition you just created is marked as *bootable*.

Once you have done this, mount this partition.

Many distro will automount your USB key volumes when you plug that in and you'll find it under `/media/your_username/volume_label`, otherwise you can mount it using:

```
sudo mkdir /mnt/my_USB_key
sudo mount /dev/sdX1 /mnt/my_USB_key
```

of course your USB device is *not* `/dev/sdX` (and partition is not `/dev/sdX1`) so change that according to your USB drive device.

Now take the ISO file for the Distro you want to install, we'll pretend it's called `SomeLinuxDistro.iso`.

So, issue the command:

```
sudo 7z x SomeLinuxDistro.iso -o/mnt/my_USB_key
```

this will extract all the files and directories from the ISO file into the partition you have prepared in the USB key (it will take a few minutes, depending on the ISO size and your USB device speed).

Done that, verify that the path `/EFI/boot` exists in your USB key and that inside it there's a file called `bootia32.efi`.

If this path doesn't exist, create these folders, if the `bootia32.efi` file is not there, download it from this very same repository (check the `files` subdir) and copy that file into the `/EFI/boot` folder of your USB key.

Finally, just ensure all the changes are written:

```
sudo sync
sudo umount /mnt/my_USB_key
```

and your USB key is now ready!

To boot from this USB key, insert it into one of your X205TA's USB ports and turn the computer on. Press `<ESC>` until the boot menu appears on screen and select the USB option.

source: https://ubuntuforums.org/showthread.php?t=2379657&p=13719125#post13719125

