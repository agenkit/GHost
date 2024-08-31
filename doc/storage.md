# Storage



## Multiple devices

Also called *arrays*. Create the `mdadm` array first, except for ZFS and Btrfs which feature built-in device management.



### `mdadm`

Canonical command: `mdadm [mode] <raiddevice> [options] <component-devices>`

#### Installation

```bash
sudo apt-get update
sudo apt-get install mdadm
```

#### Create array

Example RAID 0 over 3 devices:

```bash
sudo mdadm -Cv /dev/md0 -l0 -n3 \
/dev/nvme-xxx \
/dev/nvme-xxx \
/dev/nvme-xxx
```

- `-C` = `--create` \[mode\]
- `-v` = `--verbose`
- `/dev/md0` is the name of the new RAID device.
- `-l0` = `--level=0` specifies the RAID level of the array.  
  (Options are: `linear`, `raid0` | `0` | `stripe`, `raid1` | `1` | `mirror`, `raid4` | `4`, `raid5` | `5`, `raid6` | `6`, `raid10` | `10`, `multipath` | `mp`, `faulty`, `container`; obviously some of these are synonymous).
- `-n3` = `--raid-devices=3` tells how many drives to use for the array.
- End with the list of devices.

Check the creation of the RAID array:

```bash
cat /proc/mdstat
```

And further inspect the RAID:

```bash
sudo mdadm --detail -vv /dev/md0
```



## Filesystem

XFS (or even Ext4) is fine for most cases.  
If you have special needs, consider ZFS (especially in Raidz1-3), then Btrfs.  
*[Bcachefs](https://bcachefs.org/) is too new in my opinion.*

### XFS

#### Create the XFS Filesystem

Format the device with the XFS filesystem. Here we use a RAID device created with `mdadm`.

```bash
sudo mkfs.xfs -L data /dev/md0
```

#### Mount

Create a mount point and mount the device.

```bash
sudo mkdir /mnt/data
sudo mount /dev/md0 /mnt/data
```













### Btrfs

> [!Caution]
> **NEVER use RAID 5 or 6 with Btrfs!**  
> It's ***fundamentally* broken**.








### ZFS





