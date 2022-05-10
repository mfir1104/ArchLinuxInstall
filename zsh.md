# Install zsh

check current shell

```shell
$ echo $SHELL
```

install `zsh` packages

```shell
# pacman -S zsh
```

change zsh as default shell

```shell
$ chsh -s /bin/zsh
```

## oh-my-zsh

install oh-my-zsh

```shell
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## zsh-syntax-highlighting

install zsh-syntax-highlighting plugin

```shell
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

activate the plugin in `~/.zshrc`

```
plugins=( 
    # other plugins...
    zsh-syntax-highlighting
)
```

## zsh-autosuggestions

install zsh-autosuggestions plugin

```shell
$ git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

activate the plugin in `~/.zshrc`

```
plugins=( 
    # other plugins...
    zsh-autosuggestions
)
```

## zsh-completions

install zsh-completions plugin

```shell
$ git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
```

add it to `FPATH` in your `.zshrc` by adding the following line before `source "$ZSH/oh-my-zsh.sh"`:

```
fpath+=${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions/src
```

## zsh-history-substring-search

install zsh-history-substring-search plugin

```shell
$ git clone https://github.com/zsh-users/zsh-history-substring-search ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-history-substring-search
```
    
activate the plugin in `~/.zshrc`

```
plugins=( 
    # other plugins...
    zsh-history-substring-search
)
```

## PowerLevel10k

install PowerLevel10k

```shell
$ git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

install nerd font

```shell
$ paru nerd font
```
    
set `ZSH_THEME="powerlevel10k/powerlevel10k"` in `~/.zshrc`

type `p10k configure` if the configuration wizard doesn't start automatically
