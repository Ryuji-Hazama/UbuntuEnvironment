# Creating a Live USB Drive

You need to run the most of the command as a root (or using `sudo`.)

## Prepareing

### Download ISO Images

First of all, you need to download ISO image from [Ubuntu official web site](https://ubuntu.com/download) for Ubuntu, or any other desire ISOs.

### USB Drive

Second, you need an empty or unused USB drive with the disk size large enough to save the ISO image file.

#### Find USB Drive path

Before starting, you need to find and confirm the USB Drive path to prevent accidentally overwrite the internal disks.

Before stick your drive to your device, run `lsblk` and check the current status. Then, stick the drive to your device, and run `lsblk` again, and you can spot a new device appeared. (Usually, something like `/dev/sdb` or `/dev/sdc`) `/dev/sdX` is your drive path, and `/dev/sdX1` followng the drive path is the partition path.

#### Unmount the Drive

Ubuntu auto-mounts USB drive. To prevent error, you need to unmount the drive from the system. You can check mount path using `df` command, and unmount the drive with the following command.

```bash
umount /path/to/mount/point
```

#### Format the USB Drive

Once you find your USB drive, you need to format the drive with the following command.

```bash
mkfs.ext4 /dev/sdX
```

(Replace `X` with your drive character.)

## Single Boot Live USB

You can create a live USB using `dd` command.

```bash
dd if=/path/to/iso/file.iso of=/dev/sdX bs=4M status=progress oflag=sync
```

- `if` : Input file
- `of` : Output file
- `bs` : Read/write block size (4M for better speed)
- `status=progress` : Show the progress.
- `oflag=sync` : Ensure all data is physically written to the output path before the command finishes.

`dd` command is nicknamed `disk destroyer`, and it overwrites the drive without any warnig. *So ensure **your drive path is correct.***

## Multi Bootable USB Drive

Since the boot manager (or BIOS) cannot load multiple boot loader at once, you need to create your own bootloadere to handle boot process to each bootloaders in an ISO file.

### Partition the Drive

You need to create at least two partitions, one for bootloader and another for ISO image store.  
After format your drive, run `fdisk` to create partitions.

1. **Open the drive:** `fdisk /dev/sdX`
2. **Create a new partition table:** `g` (for GPT)
3. **Partition 1 (Bootloader):** `n`, then `1`, then `+500M` for the size (at least 100M+ for the boot partition.)
4. **Partition 2 (ISOs):** `n`, then `2`, and press Enter to use remaining space.
5. **Change type for partition 1:** `t`, then `1`, then select `1` for EFI system.
6. **Write and exit:** `w`

### Format the Partitions

Run the following commands to format each partitions.

```bash
# Partition 1: Bootloader (exFAT)
mkfs.vfat -F 32 -n BOOT /dev/sdX1

# Partition 2: ISO Storage (ext4)
mkfs.ext4 -L STORAGE /dev/sdX2
```

### Install GRUB to the Boot Partition

Use following commands to install GRUB to boot partition.

```bash
# Mount partitions before start
mkdir -p /mnt/usb/bootpart /mnt/usb/isopart
mount /dev/sdX1 /mnt/usb/bootpart
mount /dev/sdX2 /mnt/usb/isopart

# Install GRUB for UEFI
grub-install --target=x86_64-efi --efi-directory=/mnt/usb/bootpart --boot-directory=/mnt/usb/bootpart/boot --removable
```

### Configure `grub.cfg` File

#### Find UUID of ISO part

Since the ISO files are on the different partition than the bootloader, tell GRUB the ISO partition using UUID.

Run `lsblk -no UUID /dev/sdX2` to find UUID.

#### Create the config

Create a configuration file: `/mnt/usb/bootpart/boot/grub/grub.cfg`

```text
set timeout=10
set default=0

search --no-floppy --fs-uuid --set=isopart YOUR-UUID-HERE

menuentry "Ubuntu Desktop 24.04" {
        set isofile="/iso/ubuntu-24.04.4-desktop-amd64.iso"
        set iso_path="$isofile"
        export iso_path
        immod tpm
        search --set=root --file "$isofile"
        loopback loop_desktop $isofile
        set root=(loop_desktop)
        configfile /boot/grub/loopback.cfg
}

menuentry "Ubuntu Server 24.04" {
        set isofile="/iso/ubuntu-24.04.4-live-server-amd64.iso"
        set iso_path="$isofile"
        export iso_path
        immod tpm
        search --set=root --file "$isofile"
        loopback loop_server $isofile
        set root=(loop_server)
        configfile /boot/grub/loopback.cfg
}

menuentry "Restart System" {
    reboot
}

menuentry "Shut down" {
    halt
}
```

#### `grub.cfg`, `loopback.cfg` inside ISO

The `loopback.cfg` or sometimes `grub.cfg` inside the ISO file is the configuration file for the bootloader inside the ISO. The file paths may vary depending on the ISO. To find the correct path, you can mount the ISO file and explore its contents.

```bash
mkdir -p /mnt/iso
mount -o loop /path/to/iso/file.iso /mnt/iso
```

The you can find the configuration file inside the mounted ISO using `find` or something like that.

### Store ISO files

Move your ISOs to the second partition

```bash
cp /path/to/isos/* /mnt/usb/isopart
```

## Safely Remove Drive

Before removing the USB drive, you must run the following commands to ensure the drive is not in use and safe to remove.

```bash
sync
eject /dev/sdX
```
