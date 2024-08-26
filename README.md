# GHost

Graphical Host (`GHost`):
- Virtualization-ready workstation (Linux KVM)
- PCIe passthrough (for GPU, TPU, USB, NVMe…)
- "Type-II hypervisor" setup (the host is a fully usable desktop)

<!--
> [!Tip]
> See the "Server Host" (`SHost`) repository for the headless variant, without graphical DE on the host (closer to a "Type-I hypervisor"). Note that both GHost & SHost allow for graphical guests.
-->

## TODO

- [ ] Install Linux (I use Kubuntu 24.04)
- [ ] Security (1): browser, secrets, SSH server
- [ ] Terminal (1): Zsh, OMZ, bat, tldr, etc.
- [ ] IOMMU
- [ ] Libvirt hooks (PCIe hand-off)
- [ ] VM (1) creation (VirtManager)
    - [ ] Guest OS `.iso` download
    - [ ] *\[Windows guest\]* `virtio` drivers download
    - [ ] …
    - [ ] *\[Nvidia GPU\]* Error 43 workaround
- [ ] Performance tweaks
    - [ ] hugepages
    - [ ] CPU governor
    - [ ] CPU pinning
    - [ ] Disk tuning
    - [ ] *\[Windows guest\]* Hyper-V tuning
- [ ] 

## GHost setup

This guide gets you there as fast as possible. Links are provided in Footnotes and Resources below. See `somepage.md` for deeper discussion.

### Linux


### Security (1)

### Terminal (1)

### IOMMU

### Libvirt hooks

### VM (1) creation

### Performance tweaks











## Footnotes





## Resources


