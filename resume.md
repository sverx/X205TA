Touchpad might not be working when resuming after suspension.

A possible solution might be to add a systemd script to unload and reload the *elan_i2c* module when resuming.

Add a script in `/lib/systemd/system-sleep/`, for instance `resume.sh`:

```
#!/bin/sh

PATH=/sbin:/usr/sbin:/bin:/usr/bin

case "$1" in
    pre)
            #code execution BEFORE sleeping/hibernating/suspending
    ;;
    post)
            #code execution AFTER resuming
            sudo rmmod elan_i2c && sudo modprobe elan_i2c
    ;;
esac

exit 0
```

remember to set the execute bit on the script with `sudo chmod +x /lib/systemd/system-sleep/resume.sh`

original source: https://ubuntuforums.org/showthread.php?t=2254322&page=220&p=14106356#post14106356

original source: https://askubuntu.com/questions/1313479/correct-way-to-execute-a-script-on-resume-from-suspend/1318782#1318782

