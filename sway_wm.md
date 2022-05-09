# Install sway window manager

```shell
paru -S sway swaylock swayidle
```

copy config file

```shell
mkdir -p ~/.config/sway
cp /etc/sway/config ~/.config/sway
```

automatically start sway on TTY login, add the following lines to `~/.zshrc`

```
if [ -z $DISPLAY ] && [ "$(tty)" = "/dev/tty1" ]; then
  exec sway
fi
```

HiDPI, find display name using `swaymsg -t get_outputs`

edit `~/.config/sway/config`

```
output HDMI-A-1 mode 3840x2160 position 0,0 scale 1.5
```

clipboard, install `clipman`

edit `~/.config/sway/config`

```
exec wl-paste -t text --watch clipman store --no-persist
```

initially enable numlock

edit `~/.config/sway/config`

```
input * xkb_numlock enable
```
