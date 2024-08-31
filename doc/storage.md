# Storage



## Multiple devices

Also called arrays. For most filesystems, you want to create the `mdadm` array first. ZFS and Btrfs feature built-in device management.



### `mdadm`

Canonical command: `mdadm [mode] <raiddevice> [options] <component-devices>`

#### Installation

```bash
sudo apt-get update
sudo apt-get install mdadm
```

#### Create array

```bash
sudo mdadm -Cv /dev/md0 -l0 -n3 \
/dev/nvme-xxx \
/dev/nvme-xxx \
/dev/nvme-xxx
```

- `-C` = `--create` \[mode\]
- `-v` = `--verbose`
- `/dev/md0` is the name of the new RAID device.
- `-l0` = `--level=0` specifies a RAID 0 array.  
  Options are: `linear`, `raid0` | `0` | `stripe`, `raid1` | `1` | `mirror`, `raid4` | `4`, `raid5` | `5`, `raid6` | `6`, `raid10` | `10`, `multipath` | `mp`, `faulty`, `container` (obviously some of these are synonymous).
- `-n3` = `--raid-devices=3` tells how many drives to use for the array.
- End with the list of devices.





## Filesystem


### XFS














### Btrfs










### ZFS





