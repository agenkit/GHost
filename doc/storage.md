# Storage

Topics: [Filesystem](#filesystem), [Arrays](#arrays).

> [!Note]
> In this doc, we manage a hypothetical storage unit called `data`, to be mounted at `/mnt/data`.


















## Filesystem

[XFS](#xfs) (or even Ext4) is fine for most cases.

If you have special needs:
- consider [ZFS](#zfs) (raid z is great),
- then [Btrfs](#btrfs) (but **NEVER** in raid 5 or 6).  

*[Bcachefs](https://bcachefs.org/) is too new in my opinion.*

### XFS

#### Create the XFS filesystem

Format the device with the XFS filesystem.  
*Here we use a RAID device (`/dev/md0`) previously created with [`mdadm`](#mdadm).*

```bash
sudo mkfs.xfs -L data /dev/md0
```

#### Mount

Create a mount point,  
then mount the device.

```bash
sudo mkdir /mnt/data
sudo mount /dev/md0 /mnt/data
```












### ZFS










### Btrfs

> [!Caution]
> **NEVER use RAID 5 or 6 with Btrfs!**  
> It's ***fundamentally* broken**.

















## Arrays

Create the `mdadm` array first, except for ZFS and Btrfs which feature built-in device management.



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


#### Formatting

See Filesystems. If in doubt, use [XFS](#xfs).

#### Mount

Create a mount point and mount the device.

```bash
sudo mkdir /mnt/data
sudo mount /dev/md0 /mnt/data
```

#### `fstab` (boot mount)

Get the UUID of the device:

```bash
sudo blkid | grep <your-device>
```

Edit `/etc/fstab` to add an entry for the RAID 0 array:

```bash
sudo nano /etc/fstab
```

Add this line (replace `UUID` with the actual UUID from the `blkid` command):

```
UUID=<your-uuid> /mnt/data xfs defaults 0 0
```







