# Install alacritty

install alacritty

```shell
# pacman -S alacritty
```

copy config file

```shell
mkdir -p ~/.config/alacritty
cp /usr/share/doc/alacritty/example/alacritty.yml ~/.config/alacritty
```

set alacritty as default terminal emulator in sway

edit `~/.config/sway/config`

```
set $term alacritty
```

spawn new instance in same directory

edit config file `~/.config/alacritty/config`

```
key_bindings:
  - { key: Return,   mods: Control|Shift, action: SpawnNewInstance }
```
