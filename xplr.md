# XPLR

install xplr

```shell
# pacman -S xplr
```

configuration

```shell
$ mkdir -p ~/.config/xplr
$ version="$(xplr | grep ^version: | cut -d' ' -f 2)"

# When the app loads, press `#`

$ echo version = '"'${version:?}'"' > ~/.config/xplr/init.lua
$ cat /usr/share/xplr/examples/init.lua >> ~/.config/xplr/init.lua
```
