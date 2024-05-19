The WiFi adapter *should* work out-of-the-box on reasonably recent kernels but here's a fix if it doesn't work instead:

```
# be sure the EFI variables are properly exposed to userspace
sudo modprobe efivarfs

# mount efivars where we can access the contents
sudo mount -t efivarfs efivarfs /sys/firmware/efi/efivars

# then copy the file whose name starts with nvram to /lib/firmware/brcm/
sudo cp -a /sys/firmware/efi/efivars/nvram* /lib/firmware/brcm/brcmfmac43340-sdio.txt

# reload the brcmfmac module
sudo rmmod brcmfmac
sudo modprobe brcmfmac
```

Also, should the WiFi adapter not pick any 5GHz WiFi networks, this also can be fixed simply by replacing the `brcmfmac43340-sdio.txt` file with the one from this very same repository (check the `files` subdir) and copy that `brcmfmac43340-sdio.txt` file into your `/lib/firmware/brcm/` folder and then reloading the *brcmfmac* module:

```
sudo rmmod brcmfmac
sudo modprobe brcmfmac
```

original source: https://ubuntuforums.org/showthread.php?t=2379657&p=13719125#post13719125

