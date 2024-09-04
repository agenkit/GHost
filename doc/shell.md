# Shell

A shell (`bash`, `zsh`…) within a terminal emulator (Konsole, urxvt, Kitty…) is a software application that provides a command-line interface for users to interact with the operating system, enabling them to execute commands, run programs, and manage system resources.










## OS (shell-agnostic)



### Change default user shell

Use `chsh -s path/to/shell` .

For instance, to set Zsh as default:

```sh
chsh -s $(which zsh)
```

You may also want to change the startup command, for instance in your Konsole profile:  
**replace `/bin/bash` with `/bin/zsh`**.

Then logout ( <kbd>Ctrl</kbd> + <kbd>D</kbd> ),  
and login again (relaunch your terminal emulator).










## Zsh

[Zsh](https://zsh.sourceforge.io/) is great for IT people and programmers.

### Installation

```sh
sudo apt install zsh zsh-doc
zsh --version    # Confirm proper installation
```

[Change your default user shell](#change-default-user-shell) if needed. Otherwise simply run `zsh` (right within `bash` or any other shell) to use it.

You will be greeted by the Z Shell config function for new users, `zsh-newuser-install`.



### Resources

- [The Z Shell Manual](https://zsh.sourceforge.io/Doc/Release/index.html)










## Spaceship Prompt

https://spaceship-prompt.sh/

### Installation

https://spaceship-prompt.sh/getting-started/#__tabbed_1_3

1. Clone the repo:

   ```sh
   git clone https://github.com/spaceship-prompt/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt" --depth=1
   ```

1. Symlink `spaceship.zsh-theme` to your oh-my-zsh custom themes directory:

   ```sh
   ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
   ```

1. Set `ZSH_THEME="spaceship"` in your `.zshrc`.

   ```sh
   nano ~/.zshrc
   ```





