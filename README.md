# Howto: Deploy Alpine Linux on a headless Raspberry Pi

The [Alpine Linux
documentation](https://docs.alpinelinux.org/user-handbook/0.1a/Installing/setup_alpine.html)
assumes you have a way to access your system directly, such as via a display
and keyboard. However, there are many cases where you might want to deploy a
headless box, such as on a Raspberry Pi connected via ethernet (or wifi).

There are instructions out there for how to install [Alpine Linux on a
Raspberry Pi](https://wiki.alpinelinux.org/wiki/Raspberry_Pi), including how to
deploy on a [headless Raspberry
Pi](https://wiki.alpinelinux.org/wiki/Raspberry_Pi_-_Headless_Installation),
but I found them to be somewhat out of date and inconsistent. 

This repo packages those instructions into a single howto. I have also updated
the [Alpine Wiki
instructions](https://wiki.alpinelinux.org/wiki/Raspberry_Pi_-_Headless_Installation).

## Tested on

* Alpine Linux 3.14.
* Raspberry Pi 4 Model B - 4GB.

## 1 - Prepare the SD card

The SD card needs at least 2 partitions, one FAT32 boot partition and then at
least 1 more Linux type partition. This assumes you have installed `fdisk` and
want to wipe the SD card.

1. Check the name of the device using `fdisk -l` - in my case it is
   `/dev/mmcblk0`.
2. Launch fdisk against the device: `fdisk /dev/mmcblk0`
3. If necessary, remove all existing partitions using the `d` command.
4. Use the `n` command to create a new primary partition in position 1. Accept
   the default start placement and set `+256M` for the end position. Use the
   `t` command to set the type to `WIN 95 FAT32 (LBA)`.
5. Use the `n` command to create a second primary partition in position 2.
   Accept the default start placement and allow it to use the remainder of the
   disk. Use the `t` command to set the type to `Linux`.
6. Use the `a` command to toggle the partition as bootable.
7. Enter `w` to write the changes to the SD card.
8. Assuming you now have two partitions called `/dev/mmcblk1p1` and
   `/dev/mmcblk1p2` (check with `fdisk -l`), create the filesystem:
```
mkdosfs -F 32 /dev/mmcblk1p1
mkfs.ext4 /dev/mmcblk1p2
```

## 2 - Copy the Alpine system to the SD card

You now need to copy the latest Alpine download to the bootable partition on
the SD card and create the headless configuration overlay file. The overlay is
the directory structure from `overlay/` in this repo. A pre-packaged version is
provided in the releases for this repo.

1. Mount the disk: `mkdir /mnt/sd1` then `mount /dev/mmcblk1p1 /mnt/sd1`.
2. `cd /mnt/sd1` then [download the latest Alpine
   release](https://alpinelinux.org/downloads/). For a Raspberry Pi 4 this is
   the `aarch64` Raspberry Pi release.
3. Extract the tar to the current directory: `tar xvfz
   alpine-rpi-3.14.0-aarch64.tar.gz`
4. Download the headless overlay file from the releases of this repo. This
   should then exist at `/mnt/sd1/headless.apkovl.tar.gz`. Do not extract it.
5. Unmount: `umount /mnt/sd1`

### Optional: Wi-Fi config

If you want the Raspberry Pi to connect to the network over wifi, before you
unmount, create a file called `/mnt/sd1/wifi.txt` with the format:

```
ssid password
```

Otherwise if you do not create this file, the default connection will be wired.

## 3 - Boot the Raspberry Pi

In the past it was necessary to run the manual setup scripts, but Alpine now
correctly detects the Raspberry Pi and knows that SSH is already running. The
[standard setup
script](https://docs.alpinelinux.org/user-handbook/0.1a/Installing/setup_alpine.html)
can therefore be used to finish the configuration:

1. Insert the SD card into the Raspberry Pi and then boot it up. Check your
   router to find out what IP address gets assigned.
2. SSH to the appropriate IP address as root with no password. Alpine will be
   running in [diskless
   mode](https://wiki.alpinelinux.org/wiki/Installation#Diskless_Mode).
3. Run `setup-alpine` and follow the instructions. It will report there are no
   available disks and ask you to pick one. Assuming your SD card device is
   called `mmcblk0` then choose that device. It will ask you if you want to
   erase the disk and then configure it as a persistent disk.
4. Reboot.
5. Create a new user to log into future sessions as.

## Overlay directory structure

```
└── etc
    ├── .default_boot_services
    ├── local.d
    │   └── headless.start
    └── runlevels
        └── default
            └── local -> /etc/init.d/local

4 directories, 3 files
```

## Packaging the overlay

```
cd overlay
chmod +x etc/local.d/headless.start
tar czvf headless.apkovl.tar.gz etc/
```

## Credits

Thanks for the original instructions from the [Alpine Wiki user
sodface](https://wiki.alpinelinux.org/w/index.php?title=Raspberry_Pi_-_Headless_Installation&action=history),
who also hosted the overlay files on their website
([download](http://www.sodface.com/repo/headless.apkovl.tar.gz)). I expanded
and updated these instructions and then added them to GitHub so they can be
easily accessed.
