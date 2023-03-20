The Asus X205TA has 2 GiB of RAM and you can't install more than that. This amount of RAM is pretty low nowadays, so we surely need some swap (how to setup swap is OT here).

But the X205TA eMMC is *very* slow so we want to make sure we perform I/O as rarely as possible.

One option could be to use Linux's zswap kernel module. It basically creates a *compressed* swap *cache*: instead of swapping to eMMC you will (often) have zswap compress the memory page being removed and keep it in its pool in RAM.

To enable zswap, you have to add `zswap.enabled=1` at the end of the `GRUB_CMDLINE_LINUX_DEFAULT` in file `/etc/default/grub`, example:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1"
```

then issue `sudo grub-mkconfig` and reboot.

To check that zswap is indeed enabled you can do `cat /sys/module/zswap/parameters/enabled`: 'Y' means enabled, 'N' means something hasn't been set up correctly, so you need to verify.

Also, we need to make sure to lower the chance of a page swap by lowering the swappiness of the system (how to do this is OT here, there's plenty of information on the Internet about it).

A good value for vm.swappiness on a 2 GiB RAM system (with zswap enabled) seems to be 25, according to my tests. You might experiment with it, finetuning it from 15 to 35 to your liking.


Advanced usage
==============

There are a few advanced parameters we could tweak to improve zswap effectiveness.

One option is to allow the swap cache to grow bigger, up to use 50% of our RAM (as default is up to 20%) by using the parameter `zswap.max_pool_percent`. To do this you need to update the line `GRUB_CMDLINE_LINUX_DEFAULT` in file `/etc/default/grub` again, example:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash zswap.enabled=1 zswap.max_pool_percent=50"
```

remember to issue `sudo grub-mkconfig` and reboot.

To check that zswap's pool is indeed changed you can do `cat /sys/module/zswap/parameters/max_pool_percent` and it should show 50.

I don't suggest using a much higher value, as some RAM needs to be kept available to actual processes.


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

