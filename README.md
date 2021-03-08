# pi-reboot-button-service
Setup a systemd service that reboots the pi when a push button is pressed and held for some interval

Assuming you already have python3 installed

## Install pigpiozero
```
sudo apt install python3-gpiozero
```

## Add your reboot script to /usr/local/bin/reboot_button.py
```python
from gpiozero import Button
from subprocess import check_call
from signal import pause

def shutdown():
    check_call(['reboot'])

shutdown_btn = Button(17, hold_time=5)
shutdown_btn.when_held = shutdown

pause()
```

## Hookup your push button
Connect a momentary push button between a Ground Pin (e.g. Pi Header Pin 9) and a GPIO pin (Pi Header 11 / BCM 17 in this example)

## Add a systemd service file to /etc/systemd/system/reboot-button.service
```
[Unit]
Description=Reboot if GPIO button betwwen GND and BCM 17 is held for 5 seconds

[Service]
Type=simple
ExecStart=/usr/bin/env python3 /usr/local/bin/reboot_button.py

[Install]
WantedBy=multi-user.target
```

## Enable and Start your service
```
sudo systemctl enable reboot-button.service
sudo systemctl start reboot-button.service
```

## Test your reboot button
Holding down the button for 5 seconds should cause the pi to reboot itself.
