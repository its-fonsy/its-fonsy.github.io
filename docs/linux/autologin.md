# Autologin

## Autologin on TTY

The first thing to do is to enable automatic login to virtual console[^1], to do
so we must create a drop-in file[^2] called "auotologin.conf" for
`getty@tty.service`. To do this run
```bash
systemctl edit getty@tty.service --drop-in=autologin
```
then, between the first two comments add
```txt
[Service]
ExecStart=
ExecStart=-/sbin/agetty -o '-p -f -- \\u' --noclear --autologin <username> %I $TERM
```
and change "<username>" with the user that you want to login with.

### Start window manager

The previous step only autologin into tty, to start a window manager for X add
the following content[^3] to `~/.profile` (or `~/.zprofile` if using zsh)

```bash
if [ -z "$DISPLAY" ] && [ "$XDG_VTNR" = 1 ]; then
  exec startx
fi
```

If using Wayland add this content instead in `~/.profile` (or `~/.zprofile`)
```bash
if [ -z "$WAYLAND_DISPLAY" ] && [ -n "$XDG_VTNR" ] && [ "$XDG_VTNR" -eq 1 ] ; then
    exec <window-manager>
fi
```
and replace "<window-manager>" with the respective WM.
For example to autologin with sway[^4]

```bash
if [ -z "$WAYLAND_DISPLAY" ] && [ -n "$XDG_VTNR" ] && [ "$XDG_VTNR" -eq 1 ] ; then
    exec sway
fi
```

## Autologin with LightDM[^5]

Edit the LightDM configuration file and ensure these lines are uncommented and
correctly configured:

```ini title="/etc/lightdm/lightdm.conf"
[Seat:*]
autologin-user=<user>
```

You must be part of the autologin group to be able to login automatically
without entering your password:

```bash
$ sudo su
# groupadd -r autologin
# gpasswd -a <user> autologin
```

LightDM logs in using the session specified in the `~/.dmrc` of the user
getting logged in automatically. To override this file, specify
`autologin-session` in `lightdm.conf`:
```ini title="/etc/lightdm/lightdm.conf"
[Seat:*]
autologin-user=<user>
autologin-session=<session>
```
The list of valid session names for X's can be found by listing
```bash
ls /usr/share/xsessions/*.desktop
```
and for Wayland's
```bash
ls /usr/share/wayland-sessions/*.desktop
```

[^1]: [Arch wiki: systemd drop in files](https://wiki.archlinux.org/title/Systemd#Drop-in_files)
[^2]: [Arch wiki: automatic login ot virtual console](https://wiki.archlinux.org/title/Getty#Automatic_login_to_virtual_console)
[^3]: [Arch wiki: Autostart X at login](https://wiki.archlinux.org/title/Xinit#Autostart_X_at_login)
[^4]: [Arch wiki: Automatically on TTY login](https://wiki.archlinux.org/title/Sway#Automatically_on_TTY_login)
[^5]: [Arch wiki: LightDM - Enabling autologin](https://wiki.archlinux.org/title/LightDM#Enabling_autologin)
