# GHost

Graphical Host (`GHost`):
- Virtualization-ready workstation (Linux KVM)
- PCIe passthrough (for GPU, TPU, USB, NVMe…)
- "Type-II hypervisor" setup (the host is a fully usable desktop)
- Tested on Kubuntu 24.04 (should work on recent Debian & Ubuntu)


> [!Tip]
> - See the "Server Host" (`SHost`) repository for the headless variant, without graphical DE on the host (closer to a "Type-I hypervisor").   
> Note that both GHost & SHost allow for graphical guests.
> - See [`disc.md`](disc.md) for in-depth discussion about this setup.
> - *TMI*[^1] is provided in [Resources](#resources) (and footnotes).


## Setup

This guide gets you there **as fast as possible**.

- Instructions only (no explanation).
- Each step (*1, 2… n*) is a single action (glancing is OK, no hidden step in sentences).
- Direct links whenever possible.
- Copy-ready commands (if you set `$VARIABLES` correctly).


### Linux

> [!IMPORTANT]
> 🔗 Kubuntu 24.04 [`.iso`](https://kubuntu.org/getkubuntu/) (direct link)  
> 🔗 Balena Etcher [releases](https://github.com/balena-io/etcher/releases)  

1. Download the Kubuntu `.iso` .

2. If necessary, download latest Etcher release for your *current* OS (where you will flash the `.iso` to USB).

3. Install Etcher.

   ```bash
   sudo apt install ./balena-etcher_******_amd64.deb
   ```

4. Launch Etcher.

5. Select the iso, USB stick, and flash it.

6. Shutdown the PC.

7. Unplug (physically) all video outputs, except the host's. *In this guide, it's the Ryzen iGPU.*

8. Boot to USB then proceed with Kubuntu setup.

> [!CAUTION]
> Btrfs is required for some features of GHost. Make sure your root partition is formatted a

   When completed, boot on the installed system to be greeted by the KDE welcome wizard.



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



[^1]: TMI: Too Much Information.

[^2]: Generally, unplug all non-host devices during host OS installation. This ensures that, later on:
  - proper *graphics* drivers will get installed on the host;
  - auto-configs (Xorg…) work well;
  - guest GPU is available for passthrough. *In this guide, it's the Nvidia dGPU.*

