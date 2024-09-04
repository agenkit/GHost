# Storage

*Basic procedures to manage filesystems relevant to GHost (XFS, Btrfs, and ZFS) in a number of ways including software RAID.*

> [!Note]
> In this doc, we manage a hypothetical  
> - storage unit called `data`  
> - located at `/mnt/data`  
> - composed of physical drives aliased `$disk1`, `$disk2`, …, `$diskN`

## Quick Start (TL;DR)

Setup XFS in RAID `0` over `3` drives.  
*Just change these values (`0`, `3`) for variations.*

1. Install `mdadm`.

```bash
sudo apt install mdadm
```

2. Create a RAID `0` array with `3` drives.

```bash
sudo mdadm -Cv /dev/md0 -l0 -n3 $disk{1,2,3}
```

3. Check up. <= do this often!

```bash
cat /proc/mdstat
sudo mdadm --detail -vv /dev/md0
```

4. Format the RAID device.

```bash
sudo mkfs.xfs -L data /dev/md0
```

5. Mount the RAID device to a new directory.

```bash
sudo mount -mo noatime,logbsize=256k /dev/md0 /mnt/data
```

6. Setup mounting at boot time.

```bash
sudo blkid | grep md0
sudo nano /etc/fstab
UUID=<your-uuid> /mnt/data xfs defaults,noatime,logbsize=256k 0 0
```




## What you need to know

- \[optional\] **Multiple drives** must *first* be grouped in an [array](#arrays).
- A physical drive must be **formatted** using a [filesystem](#filesystem).
- A formatted filesystem must finally be [mounted](#mount) to **read/write** it.

For [ZFS](#zfs) and [Btrfs](#btrfs), these principles stand, but head over to their dedicated section as they manage things their own way.




















## Arrays

Create the `mdadm` array first, except for ZFS and Btrfs which feature built-in device management.



### `mdadm`

`mdadm` *(**m**ultiple **d**evices **adm**inistration) lets us "merge" storage devices in RAID arrays, which are then seen (and formatted) as one virtual device (for increased higher size, reliability and/or performance).*

📘`man`: [`mdadm`][man-mdadm]

Canonical command: `mdadm [mode] <raiddevice> [options] <component-devices>`



#### Installation

```bash
sudo apt update
sudo apt install mdadm
```

#### Create array

The **create** mode (`-C`|`--create`) will make:

- a Linux software **RAID device** (virtual device `/dev/md0`)
- over **`n` component devices** (`n` physical drives)
- set to RAID **level `l`** (`l`=`0`,`1`,`10`,`5`,`6`)

Example RAID level `0` over n=`3` devices:

```bash
sudo mdadm -Cv /dev/md0 -l0 -n3 \
/dev/disk/by-id/nvme-Samsung_SSD*{497K,512J,528K}
```

- `-v` = `--verbose`
- `-l0` = `--level=0` options are:  
`linear`,  
`raid0` | `0` | `stripe`,  
`raid1` | `1` | `mirror`,  
`raid4` | `4`,  
`raid5` | `5`,  
`raid6` | `6`,  
`raid10` | `10`,  
`multipath` | `mp`,  
`faulty`,  
`container`.  
Obviously, some of these are synonymous.

Check the creation of the RAID array:

```bash
cat /proc/mdstat
```

And further inspect the RAID:

```bash
sudo mdadm --detail -vv /dev/md0
```








## Filesystem

[XFS](#xfs) (or even Ext4) is fine for most cases.

If you have special needs:
- consider [ZFS](#zfs) (raid z is great),
- then [Btrfs](#btrfs) (but **NEVER** in raid 5 or 6).  

*[Bcachefs](https://bcachefs.org/) looks promising but is too new in my opinion.*




### XFS

📘`man`: [`xfs`][man-xfs], [`mkfs.xfs`][man-mkfs.xfs]



#### Create the XFS filesystem

Format the device with the XFS filesystem.  
*Here we use a RAID device `/dev/md0` previously created with [`mdadm`](#mdadm).*

```bash
sudo mkfs.xfs -L data /dev/md0
```



#### XFS over `mdadm`

On modern Linux, leave it to defaults, unless you really know why.

> `xfsprogs` and the `mkfs.xfs` utility automatically select the best stripe size and stripe width for underlying devices that support it, such as Linux software RAID devices.
>
> …
>
> To check the parameters in use for an XFS filesystem, use xfs_info.
>
> ```bash
> xfs_info /dev/md0
> ```
>
> ```
> meta-data=/dev/md0               isize=256    agcount=32, agsize=45785440 blks
>          =                       sectsz=4096  attr=2
> data     =                       bsize=4096   blocks=1465133952, imaxpct=5
>          =                       sunit=16     swidth=48 blks
> naming   =version 2              bsize=4096   ascii-ci=0
> log      =internal               bsize=4096   blocks=521728, version=2
>          =                       sectsz=4096  sunit=1 blks, lazy-count=0
> realtime =none                   extsz=196608 blocks=0, rtextents=0
> ```
>
> 🔗 <https://raid.wiki.kernel.org/index.php/RAID_setup#XFS>


#### `mount` options

Generally keep defaults.


##### `discard`|`nodiscard`

Note: It is currently recommended that you use the `fstrim` application to discard unused blocks rather than the `discard` mount option because the performance impact of this option is quite severe. For this reason, **`nodiscard`** is the **default**.


##### `logbsize=value`

Set the size of each in-memory log buffer.  
Valid sizes for version 2 logs also include `65536` (value=`64k`),`131072` (value=`128k`) and `262144` (value=`256k`).

The  default value for version 1 logs is `32768`, while the default value for version 2 logs is `max(32768, log_sunit)`.


##### Deprecated

Smell obsolete guides.

```
Name                        Removed
----                        -------
delaylog/nodelaylog         v4.0
ihashsize                   v4.0
irixsgid                    v4.0
osyncisdsync/osyncisosync   v4.0
barrier/nobarrier           v4.19
```


#### xfsdump

> Each XFS filesystem is labeled with a Universal Unique Identifier (UUID). The UUID is stored in every allocation group header and is used to help distinguish one XFS filesystem from another, therefore you should  avoid  using `dd(1)` or other block-by-block copying programs to copy XFS filesystems.
>
> …
>
> **`xfsdump(8)` and `xfsrestore(8)` are recommended for making copies of XFS filesystems.**


#### File attributes

The XFS filesystem supports setting the following file attributes on Linux systems using the [`chattr(1)`][man-chattr] utility:

`a` - append only  
`A` - no atime updates  
`d` - no dump  
`i` - immutable  
`S` - synchronous updates  



### ZFS

…








### Btrfs

> [!Caution]
> **NEVER use RAID 5 or 6 with Btrfs!**  
> It's ***fundamentally* broken**.


We use [`block-group-tree`](https://btrfs.readthedocs.io/en/latest/mkfs.btrfs.html#filesystem-features) to *"greatly reduce mount time for large filesystems."*

Note that we'll have to [disable COW](https://wiki.archlinux.org/title/Btrfs#Disabling_CoW) for the VM image directory using `chattr +C /path/to/dir` to avoid a useless performance hit.




Great blog: [Forza's ramblings](https://wiki.tnonline.net/w/Category:Btrfs)











## Mount

📘`man`: [`mount`][man-mount]

Canonical form: `mount [-fnrsvw] [-t fstype] [-o options] device mountpoint`



### Basic use

A formatted device can be mounted to a directory using `mount` as superuser.

```bash
sudo mount -vm /dev/md0 /mnt/data
```

> [!Tip]
> `-m` (`--mkdir`) creates the mount point directory if it does not exist yet.

### `fstab` (boot mount)

Get the UUID of the device:

```bash
sudo blkid | grep /dev/<your-device>
```

Edit `/etc/fstab` to add an entry for the RAID 0 array:

```bash
sudo nano /etc/fstab
```

Add this line (replace `UUID` with the actual UUID from the `blkid` command):

```fstab
UUID=<your-uuid> /mnt/data xfs defaults,noatime 0 0
```










[man-mount]: https://manpages.ubuntu.com/manpages/noble/en/man8/mount.8.html
[man-xfs]: https://manpages.ubuntu.com/manpages/noble/en/man5/xfs.5.html
[man-mkfs.xfs]: https://manpages.ubuntu.com/manpages/noble/en/man8/mkfs.xfs.8.html
[man-chattr]: https://manpages.ubuntu.com/manpages/noble/en/man1/chattr.1.html
[man-mdadm]: https://manpages.ubuntu.com/manpages/noble/en/man8/mdadm.8.html


<!--
[man-]: 
-->















