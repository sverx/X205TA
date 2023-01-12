Even though audio should work out-of-the-box, audio from headphones (and headphones detection) will not

You might need to add a quirk to fix this issue, albeit it's currently not completely fixed

Add this line into the file `/etc/modprobe.d/50-x205ta.conf` (create the file if it doesn't exist already):

```
options snd_soc_rt5645 quirk=0x31
```

Now the sound will come from the headphones when you plug them in.

Unfortunately upon unplugging, the system won't select the speaker out again, you'll have to do that yourself from the audio mixer.

original source: https://ubuntuforums.org/showthread.php?t=2254322&page=177&p=13668396#post13668396

