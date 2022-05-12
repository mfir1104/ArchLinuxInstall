# Neovim

install neovim

```shell
# pacman -S neovim
```

## Restore cursor after exit or suspend neovim

edit `~/.config/nvim/init.lua`

```
vim.cmd [[
    augroup change_cursor
        au!
        au ExitPre * :set guicursor=a:ver90
    augroup END
]]
```
