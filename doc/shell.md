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

[Change your default user shell](#change-default-user-shell) if needed.  
Otherwise simply run `zsh` (right within `bash` or any other shell) to use it.

You will be greeted by the Z Shell config function for new users, `zsh-newuser-install`.  
*Hit <kbd>q</kbd> to quit if you're going to install Oh My Zsh next.*



### Oh My Zsh!

https://ohmyz.sh/

#### Installation

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

#### Plugins

Overview: <https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins-Overview>

Full list: <https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins>

Enable a plugin by adding its name to the `plugins` array in your `.zshrc` file (found in the `$HOME` directory). For example, this enables the `rails`, `git` and `ruby` plugins, in that order:

```
plugins=(rails git ruby)
```

> [!NOTE]
> Elements in zsh arrays are separated by whitespace (spaces, tabs, newlines...).  
> **DO NOT use commas.**

#### zsh-autosuggestions

https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh

1. Clone this repository into `$ZSH_CUSTOM/plugins` (by default `~/.oh-my-zsh/custom/plugins`)

```sh
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

2. Add the plugin to the list of plugins for Oh My Zsh to load (inside `~/.zshrc`):  

```sh
plugins=( 
    # other plugins...
    zsh-autosuggestions
)
```

3. Restart zsh (such as by opening a new instance of your terminal emulator).

#### zsh-syntax-highlighting

https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh

1. Clone this repository in oh-my-zsh's plugins directory:

```sh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

2. Activate the plugin in `~/.zshrc`.  
It **must** be last!

```sh
plugins=( [plugins...] zsh-syntax-highlighting)
```

3. Restart Zsh (such as by opening a new instance of your terminal emulator).

### Spaceship Prompt

🏛️ https://spaceship-prompt.sh/

🧬 https://github.com/spaceship-prompt/spaceship-prompt

> **Minimalistic, powerful and extremely customizable Zsh prompt**  
>
> It combines everything you may need for convenient work, without unnecessary complications, like a real spaceship."



#### Installation

🔗 https://spaceship-prompt.sh/getting-started/#__tabbed_1_3

1. Clone the repo:

   ```sh
   git clone https://github.com/spaceship-prompt/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt" --depth=1
   ```

2. Symlink `spaceship.zsh-theme` to your oh-my-zsh custom themes directory:

   ```sh
   ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
   ```

3. Set `ZSH_THEME="spaceship"` in your `.zshrc`.

   ```sh
   nano ~/.zshrc
   ```

4. Restart Zsh.





### Resources

- Zsh: [The Z Shell Manual](https://zsh.sourceforge.io/Doc/Release/index.html)

- Oh My Zsh [documentation](https://github.com/ohmyzsh/ohmyzsh/wiki)
