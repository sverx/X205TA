The integrated microSD/microSDHC/microSDXC reader *should* work out-of-the-box (kernel>=4.5.0-2) and it *should* handle memory cards of any size... however there were some reported issues with cards larger than 64 GiB, not sure what was the problem.

However, if the reader seems to do not like your memory cards, there's a possible fix:

Create a file `/etc/modprobe.d/sdhci.conf`

```
options sdhci debug_quirks=0x8000
```

Note that I couldn't verify this one myself as I only have a 16 GiB microSDHC card at hand and it just works out-of-the-box

original source: https://ubuntuforums.org/showthread.php?t=2254322&page=91&p=13470337#post13470337

original source: https://wiki.debian.org/InstallingDebianOn/Asus/X205TA

