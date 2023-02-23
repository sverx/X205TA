The X205TA has 2 GiB of RAM and you can't add more. This amount of RAM is pretty low nowadays, so we need some swap (how to setup swap is OOT here).

But the X205TA eMMC is *very* slow so we want to make sure we swap as rarely as possible.

One option could be to use Linux's zswap kernel module. It basically creates a *compressed* swap *cache*: instead of swapping to eMMC you will (often) have zswap compress the page and keep it in RAM.

To enable zswap, you have to add `zswap.enabled=1` at the end of the `GRUB_CMDLINE_LINUX_DEFAULT` in file `/etc/default/grub`, example:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1"
```

then issue `sudo grub-mkconfig` and reboot.

To check that zswap is indeed enabled you can do `cat /sys/module/zswap/parameters/enabled`: 'Y' means enabled, 'N' means something hasn't been set up correctly, so you need to verify.

Advanced usage
==============

There are a few advanced parameters we could tweak to improve zswap effectiveness.

One option is to allow the swap cache to grow bigger, up to use 50% of our RAM (as default is up to 20%) by using the parameter `zswap.max_pool_percent`. To do this you need to update the line `GRUB_CMDLINE_LINUX_DEFAULT` in file `/etc/default/grub` again, example:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1 zswap.max_pool_percent=50"
```

remember to issue `sudo grub-mkconfig` and reboot.

To check that zswap's pool is indeed changed you can do `cat /sys/module/zswap/parameters/max_pool_percent` and it should show 50.


Optional even more advanced usage
=================================

An even more advanced option is to use an improved memory allocator (*z3fold* for example) and/or an improved compression algorithm (*lz4* for example) for zswap instead of the default ones (*zbud* and *lzo*).

In this case, we need to make sure these two additional modules are included in the initramfs at boot. To do so we must edit the `/etc/initramfs-tools/modules` file and add the lines:

```
lz4
z3fold
```

then you need to issue `sudo update-initramfs -u` to update current kernel's initramfs or `sudo update-initramfs -u -k all` to update initramfs for all the installed kernels.

Now, to actually tell zswap to use those, we also need to update the line `GRUB_CMDLINE_LINUX_DEFAULT` in file `/etc/default/grub` once more, example:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1 zswap.max_pool_percent=50 zswap.zpool=z3fold zswap.compressor=lz4"
```

then of course remember to issue `sudo grub-mkconfig` and reboot.

To check that everything is correct, you can issue `grep -R . /sys/module/zswap/parameters` and the output should then be:

```
/sys/module/zswap/parameters/same_filled_pages_enabled:Y
/sys/module/zswap/parameters/enabled:Y
/sys/module/zswap/parameters/max_pool_percent:50
/sys/module/zswap/parameters/compressor:lz4
/sys/module/zswap/parameters/zpool:z3fold
/sys/module/zswap/parameters/accept_threshold_percent:90
```

sources:
 - https://ubuntu.com/blog/how-low-can-you-go-running-ubuntu-desktop-on-a-2gb-raspberry-pi-4

