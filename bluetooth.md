Bluetooth might not work out of the box (depending on kernel version).

First, you might need a firmware. Check if `BCM43341B0.hcd` is present in your `/lib/firmware/brcm/` folder. If that isn't there, copy this file from this repository or download it directly there:

```
sudo wget https://raw.githubusercontent.com/harryharryharry/x205ta-iso2usb-files/master/BCM43341B0.hcd -O /lib/firmware/brcm/BCM43341B0.hcd
```

* On kernel >=5.0.20: add a systemd service to set the Bluetooth device address.

Add a file `/etc/systemd/system/bluetooth-addr.service`:

```
[Unit]
Description=Set Bluetooth device address
After=bluetooth.service
Requires=bluetooth.service

[Service]
ExecStart=/usr/bin/btmgmt -i hci0 public-addr ###BLUETOOTH_MAC_ADDRESS###
Restart=on-failure

[Install]
WantedBy=bluetooth.target
```
replacing `###BLUETOOTH_MAC_ADDRESS###` in the file with some (any) value as `43:34:1B:01:02:03` for instance.

Then:
```
$ sudo systemctl enable bluetooth-addr.service
$ sudo systemctl start bluetooth-addr.service
```
original source: https://ubuntuforums.org/showthread.php?t=2254322&page=217&p=14022861#post14022861

* On older kernel, should Bluetooth not work out of the box: add a running btattach service.

Add a file `/etc/systemd/system/btattach.service`:

```
[Unit]
Description=Btattach

[Service]
Type=simple
ExecStart=/usr/bin/btattach --bredr /dev/ttyS1 -P bcm
ExecStop=/usr/bin/killall btattach

[Install]
WantedBy=multi-user.target
```

Then:


```
# (re)start the services with
sudo systemctl restart btattach
sudo systemctl restart bluetooth

# check the services
sudo systemctl status btattach
sudo systemctl status bluetooth
# (if the btattach service failed, check if the binary 'btattach' is installed)
# (if the service btattach failed but btattach is installed, try changing /dev/ttyS1 to /dev/ttyS4 and reload and restart the service)

# when you’re booted into linux on the ssd you can repeat step 3. and enable the services at boot with:
sudo systemctl enable btattach
sudo systemctl enable bluetooth
```

original source: https://ubuntuforums.org/showthread.php?t=2379657&p=13719125#post13719125

