# Udev

## How to let the logged in user have access to usb devices

This is done through udev rules.

Write into `/etc/udev/rules.d/10-user-usb.rules` the following content

    SUBSYSTEMS=="usb", MODE="0660", TAG+="uaccess"

For more details check the Archwiki

    https://wiki.archlinux.org/title/Udev#Allowing_regular_users_to_use_devices
