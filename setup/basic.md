# Setup Guide (basic)

Status: `v0.1` **work-in-progress**  
Target: complete PoC (1); then proper writing (2).

> [!Warning]
> Numbering, sectioning, etc. is unstable.

- [x] 1 — Install Linux (Kubuntu 24.04)
- [x] 2 — Terminal: Zsh, OMZ, bat, tldr, etc.
- [x] 3 — Security: browser, secrets, SSH server
- [ ] 4 — IOMMU
- [ ] . — Libvirt hooks (PCIe hand-off)
- [ ] . — VM (1) creation (VirtManager)
- [ ] . — Performance tweaks



---

> [!Tip]
> Footnote = **Help!**
> 🡪 *If some* `thing`[^footnote] *doesn't work, check out its footnote!*











## 1 — Host OS installation

*Two approaches for the host GUI: 'richer' (KDE, Gnome...) or 'leaner' (Mate, i3...).  
Here we go with KDE on Ubuntu, because it has many required features out of the box.*

> [!Note]
> This host OS, though perfectly usable as a workstation, will eventually operate as the lowest layer (the "hypervisor") in our single-PC virtualized infrastructure. It will sit 'underneath it all' from the standpoint of our ultimate virtual workstation. We give it GUI capabilities because it's cheap (integrated in most consumer CPUs), convenient for setup and use (as you're experiencing right now), and actually harder to avoid than not on consumer platform, whose default specs are optimized for workstation/desktop use as opposed to server (no dumb VGA or serial outputs, or adapters & devices to leverage those).  
> If you use a Epyc, Thunderbolt, or Xeon platform, YMMV; consider doing the hypervisor as a pure 'headless' server.




### Make a bootable device

1. Download the **Kubuntu [`.iso 🔽`](https://cdimage.ubuntu.com/kubuntu/releases/24.04/release/kubuntu-24.04-desktop-amd64.iso)** file.

1. Download the latest **Balena Etcher [release](https://github.com/balena-io/etcher/releases)** for your *current* OS (where you will flash the `.iso` to USB).

1. Install Etcher.

   ```bash
   sudo apt install ./balena-etcher_******_amd64.deb
   ```

1. Launch it (GUI 🖱️ app in your usual menu).

1. Flash `kubuntu-24.04-desktop-amd64.iso` onto your USB stick.

1. Shutdown the PC.




### Install Linux

1. ⚠️ **Unplug (physically) all video outputs, except the host's.**

   *In this guide, the Ryzen iGPU is dedicated to the host.  
   So we unplug all video cables going out of the Nvidia GPU.*[^unplug]

1. Boot to USB to install Kubuntu.

1. **Follow the steps** (language, kbd. layout, time zone, network, `$HOST`, `$USERNAME`…)  
until it asks you about **storage**.

1. Choose **Btrfs** for the OS root partition ("`/`").[^btrfs-root]
      
      NOTE: ⛔ **NEVER** use **RAID 5** or **6** with Btrfs, it's **fatally flawed**.  
      All manners of RAID 1 and 0 (1c3, 1c4, 10) are perfectly fine however.
      
1. Agree to install **`virt-manager`** to get the KVM/QEMU stack properly installed.
   
1. Remove the USB stick when asked to, then press <kbd>Enter</kbd>.

   *You'll reboot on the freshly installed system, to be greeted by the KDE welcome wizard.*




## Host OS configuration




### First boot

1. Upgrade packages.

   ```bash
   sudo apt update
   sudo apt upgrade
   ```

1. Open KDE Settings.

1. Ensure **Display** is fine (Xorg|Wayland; resolution, refresh rate, scaling; **Fonts**, antialiasing…)  
   *Reboot if needed.*

1. Check the **Driver Manager** for your currently in-use GPU otherwise.
   
1. Do stuff needed for you (appearance, behaviors, shortcuts, Bluetooth, etc.)




### Shell

1. Customize your terminal emulator preferences.

1. Install your preferred **CLI shell**.[^shell]

   *Suggestion:* [**Zsh**](https://github.com/agenkit/GHost/blob/main/doc/shell.md#zsh)  
   *It's easier for admin duties; allows advanced scripting (like Perl); transfers to BSD (including MacOS).*  
   *See* **[`Shell`](doc/shell.md)** *for more details.*

1. *(Optional) Play with OS & DE settings to your liking.  
Custom DNS; nice packages like `htop`, `batcat`, `tldr`; themes, etc.*




### Additional storage

**(Recommended)** 

Setup additional storage: **high IOPS** for virtualization; **large/fast** for big static files (like AI models, videos, games).

See 📜 **[Storage](doc/storage.md)** if needed, as it's hard to generalize for all cases.

Example below:

   - array of `n = 3` drives
   - RAID level `0`
   - XFS filesystem label `fs`
   - mount point `/fs`

To fit your case, you likely need to change `n`, and optionally the RAID level.

> [!Important]
> Disk names should be sourced from `/dev/disk/by-id/`  
🡢 Select names with a unique **serial_number** or `eui`

1. Install `mdadm` — **m**ultiple **d**evices **adm**inistration.

   ```sh
   sudo apt install mdadm
   ```

1. Create RAID level `0` virtual device `md0` with `3` disks.

   ```sh
   sudo mdadm -Cv /dev/md0 -l0 -n3 \
   /dev/disk/by-id/nvme-Samsung_SSD_XXX_PRO_1TB_<serial_number> \
   /dev/disk/by-id/nvme-Samsung_SSD_XXX_PRO_1TB_<serial_number> \
   /dev/disk/by-id/nvme-Samsung_SSD_XXX_PRO_1TB_<serial_number>
   ```

1. Check the array.

   ```sh
   cat /proc/mdstat
   sudo mdadm --detail /dev/md0
   ```

1. Format to XFS.

   ```sh
   sudo mkfs.xfs -L fs /dev/md0
   ```

1. Mount the RAID virtual device to a newly created (`-m` for mkdir) directory `/fs` .

   ```sh
   sudo mount -mo defaults,noatime,logbsize=256k /dev/md0 /fs
   ```

1. Then to setup boot mount, get the RAID virtual device UUID.

   ```sh
   sudo blkid | grep md0
   ```

   > 🡳 *output* 
   >
   > ```
   > /dev/md0: LABEL="fs" UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" BLOCK_SIZE="512" TYPE="xfs"
   >                            🡡                                  🡡
   >                            This is your UUID number.
   > ```

1. Copy that UUID number.

1. Open `fstab`.

   ```
   sudo nano /etc/fstab
   ```
   
1. Add this line, presumably last.

   ```
   UUID=<your UUID number> /fs xfs defaults,noatime,logbsize=256k 0 0
   ```

1. **Reboot** to check that it works.  

> [!Info]
> After reboot, the `mdadm` RAID virtual device MAY be given a different name by **udev**.  
*In my case, it's `md127` instead of `md0`.*




### Browser

1. (Optional) *Install your browser of choice.*
    
    *I use [Brave](https://brave.com/linux/#debian-ubuntu-mint). Instructions as of Sept. 2024:*

   ```bash
   sudo apt install curl

   sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg

   echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main"|sudo tee /etc/apt/sources.list.d/brave-browser-release.list

   sudo apt update
   sudo apt install brave-browser
   ```










## 2 — Security




### Secrets

1. Setup whatever means you use to **store and access secrets**.  
   *Software, pen and paper, savant memory… it's up to you.*[^secrets]

1. Fill in all **credentials for this workstation**. User password, any filesystem encryption passphrase, browser sync, etc.




### Firewall

**`ufw`**, the **U**ncomplicated **F**ire**w**all,[^ufw] is preinstalled with Ubuntu.

GUI setup:

1. Open the Settings > **Firewall** app  

1. Tick the top box to **enable** it.

1. Check that "Default **Incoming** Policy" is set to **`Ignore`**.

1. Check that "Default **Outgoing** Policy" is set to **`Allow`**.

[CLI setup](https://documentation.ubuntu.com/server/how-to/security/firewalls/):

```bash
sudo ufw enable
sudo ufw status verbose
```




### SSH server

1. Install OpenSSH, both `client` and `server`.

   ```sh
   sudo apt install openssh-client openssh-server
   ```

1. Create and edit a new config file.

   ```sh
   sudo nano /etc/ssh/sshd_config.d/nexus.conf 
   ```

1. We'll only take care of a few security aspects for now.  

   It's a good idea to document your systems, so I add a short intro.

```

# This is the sshd server nexus-wide configuration file.  See
# /fs/nexus.md for more information.

# The strategy used for options is to override the defaults at
# /etc/ssh/sshd_config with the following.

Port 60000
AddressFamily inet
ListenAddress 127.0.0.1

HostKey /etc/ssh/ssh_host_ed25519_key

AllowUsers kit
PermitRootLogin no

PasswordAuthentication no
```

*You may need to reboot for OpenSSH to work properly and accept your settings. We don't need it yet though, so it can wait.*

1. Enable `ssh` in systemd.

   ```sh
   sudo systemctl enable ssh
   ```

> [!Note]
> At this point, the ssh server isn't accessible from the outside world, on purpose.
> We'll set it up properly, along with `ufw`, later.










## 3 — IOMMU

> [!Important]
> For the next few sections, we mostly rely on **Bryan Steiner's excellent [tutorial](https://github.com/bryansteiner/gpu-passthrough-tutorial/)**.  
**Check it first** if my copycat doesn't get you through!












## Libvirt hooks

## VM (1) creation

## Performance tweaks













[^footnote]: Click the ending link to go back up where you were:

[^unplug]: Generally, unplug all non-host devices during host OS installation. This ensures that, later on:  
      - proper graphics drivers will get installed on the host (*AMD in this guide*);  
      - auto-configs (Xorg…) work well;  
      - guest GPU is available for passthrough. *In this guide, it's the Nvidia dGPU.*  

[^btrfs-root]: Gives us neat features, like easy system rollback/versioning, remote backup, later conversion of a single device to RAID 1, and more.

[^secrets]: This guide doesn't have general opinions about which solution best fits **you**, as security depends on threat profile; but most people should use **something** fitting their personal needs. Here are a few suggestions I'm using concurrently for different purposes.
      - [Proton Pass](https://proton.me/pass) as part of a [secure suite of services](https://proton.me/) including email with domain, VPN, cloud storage, doc writing, password management; and combined features such as uniquely generated email addresses per account. *Note that Proton services lack many Linux clients (Drive, Pass…), forcing you to the web interface. They notably has little to no CLI tools. That's too bad, but it's not like there are many alternatives at that level of security and privacy.*
      - [1Password](https://1password.com/) for a standalone, comprehensive secrets solution. It has extensive comfort features to store all manners of secrets, including ssh keys, payment credentials, social security and ID scans, etc.  
      - [`pass`](https://www.passwordstore.org/) (CLI tool), and [compatible](https://www.passwordstore.org/#other) apps.  
      
      Both Proton and 1Password are good for org and family admins: pass sharing, account management & retrieval…  
      You know who you are if you want/need `pass` (I consider it a better but harder solution for most purposes).

[^ufw]: 📘`man`: [`ufw(8)`][man-ufw]

[^shell]: Ricing: I use [Zsh](https://zsh.org/),  
with [Oh My Zsh](https://ohmyz.sh/) (OMZ),  
the [Spaceship Prompt](https://spaceship-prompt.sh/) theme,  
and a [NerdFont](https://github.com/ryanoasis/nerd-fonts) (preview them [here](https://www.nerdfonts.com/font-downloads)).  

  Whenever you install a new terminal tool on a machine, check for its [OMZ plugin](https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins).  
E.g., 
[`sudo`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/sudo), 
[`git`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/git), 
[`1password`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/1password), 
[`pip`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/pip), 
[`ssh`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/ssh), 
[`ssh-agent`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/ssh-agent), 
[`systemd`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/systemd) 
[`ufw`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/ufw), 
[`ubuntu`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/ubuntu), 
[`vscode`](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/vscode)…  
Don't add too many (strict need-to basis), as it may slow down shell startup.










[^SHost]: `SHost` (Server Host) is the **CLI/headless** variant.  
     - It's closer to a Type-I hypervisor.  
     - Both `GHost` & `SHost` allow for 'native' graphical guests with plugged-in display, keyboard, mouse…  
     - Both are part of my tentative [***Ultra***structure]() computing paradigm.  

[^?]: Consider using PCIe splitters if you don't have enough slots. Keep in mind that expensive PLX chips won't help for concurrent use, so I'd avoid them for GHost.


[^raid0]: Using **RAID 0 is highly discouraged** unless you do extremely regular backups, or you just plain don't care about your data.

[^xfs]: 







[man-ufw]: https://manpages.ubuntu.com/manpages/noble/en/man8/ufw.8.html
