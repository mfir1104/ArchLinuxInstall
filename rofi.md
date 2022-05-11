# Install rofi

```shell
# paru -S rofi-lbonn-wayland
```

set rofi as default application launcher in sway

edit `~/.config/sway/config`

```
set $menu rof -show combi
```

## Themes

create rofi themes directory

```shell
$ mkdir -p ~/.local/share/rofi/themes/
```

```shell
$ git clone https://github.com/lr-tech/rofi-themes-collection.git
$ cp rofi-themes-collection/themes/* ~/.local/share/rofi/themes/
```

run Rofi in `run` modi, then run `rofi-theme-selector`

search for your desired theme, press `enter` to preview, then `Alt + A` to accept the new theme.

One Dark theme

```shell
$ curl https://raw.githubusercontent.com/davatorium/rofi-themes/master/User%20Themes/onedark.rasi -o ~/.local/share/rofi/themes/onedark.rasi
```
