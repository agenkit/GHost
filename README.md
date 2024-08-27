# GHost

> GHost: the **G***raphical* **Host**

## Overview

**GHost** is a type-2 hypervisor-workstation (*hyperstation?* 🫣) designed to seamlessly orchestrate multiple PCIe devices (GPU, TPU…), across ad hoc environments (Python, Windows, servers…), in both combined and discrete operations.

In plain English, it's an always-on single box that virtualizes whatever infrastructure you want to throw at it.  
This notably includes full-fledged "native" GPU-powered workstation VMs for graphical or AI applications, with their own display, keyboard, mouse…

Tested on Kubuntu 24.04 🡪 *should* thus work on most recent Debian-based distros.


### Hardware

- Exactly **1 PC** (x64 platform)
   - … with a chipset/motherboard that supports **IOMMU** virtualization features
   - … and *enough™* **RAM** & CPU **cores** (I like 64 GB over 16 physical cores)
- At least **2 GPU** (counting iGPU, if any)
   - … with a chipset/motherboard that sports *enough™* **PCIe lanes** (usually 20 on consumer platforms)
- NVMe storage (at least for OS, host & guests)


### About this guide

This procedure gets you there **as fast as possible**.

- Instructions only (KISS, no "why")
- Atomic steps (single actions)
- Direct links (whenever possible)
- Footnotes = **Help!** 🡪 *"If **this**[^footnote] doesn't work, check out its footnote!"*

> [!Tip]
>
> This is a *one-size-fits-most* GHost setup.
> You may need additional resources for your use case.
> 
> - [Resources](#resources) has all the **reference links**: docs, repos, guides, discussions, etc.
> - [`disc.md`](disc.md) is my little book about this kind of virtualized infra. It probably contains some answers you seek.
> - [`SHost`]() (Server Host) is the **CLI/headless** variant.
>   - It's closer to a Type-I hypervisor.
>   - Both `GHost` & `SHost` allow for 'native' graphical guests with plugged-in display, keyboard, mouse…

`GHost` (**G**raphical **Host**) is part of my tentative [***Ultra***structure]() computing paradigm.

----

## Setup



### Kubuntu Linux Installation



1. Download the **Kubuntu [`.iso`](https://kubuntu.org/getkubuntu/)** file.

1. Download the latest **Balena Etcher [release](https://github.com/balena-io/etcher/releases)** for your *current* OS (where you will flash the `.iso` to USB).

1. Install Etcher.

   ```bash
   sudo apt install ./balena-etcher_******_amd64.deb
   ```

1. Launch Etcher (GUI app in your usual menu).

   1. Select the iso.
   2. Select the USB stick.
   3. Flash.

1. Shutdown the PC.

1. Unplug (physically) all video outputs, except the host's. *In this guide, it's the Ryzen iGPU.*

1. Boot to USB to setup Kubuntu. Most defaults are OK, except:

   1. **Btrfs** on the root partition (`/`) is **required** for some features (local snapshots, remote backups, easy rollback, and more).
   3. Agree to install `virt-manager` and the other thing [???] (not Krita).
   
1. Remove the USB stick when asked to, then press <kbd>Enter</kbd>.
   You'll reboot on the freshly installed system, to be greeted by the KDE welcome wizard.





### Security & Access (1)

#### Secrets



#### SSH server



### Terminal (1)

### IOMMU

### Libvirt hooks

### VM (1) creation

### Performance tweaks










## Resources


- [Kubuntu](https://kubuntu.org/)
- [Etcher](https://etcher.io/)
- [Btrfs](https://btrfs.readthedocs.io/en/latest/)



- [Rsync](https://rsync.samba.org/)
   - [Repository](https://github.com/RsyncProject/rsync)




[^footnote]: Then hit the back link to get back where you were:

[^1]: TMI: Too Much Information

[^2]: Generally, unplug all non-host devices during host OS installation. This ensures that, later on:
  - proper *graphics* drivers will get installed on the host;
  - auto-configs (Xorg…) work well;
  - guest GPU is available for passthrough. *In this guide, it's the Nvidia dGPU.*


[^?]: Consider using PCIe splitters if you don't have enough slots. Keep in mind that expensive PLX chips won't help for concurrent use, so I'd avoid them for GHost.














