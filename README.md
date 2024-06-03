# QEMU ARM Machine Project

This project contains a script and example files to set up and boot an ARM
machine using QEMU. It includes a bash script to manage the virtual machine,
create an SD card image, and provide necessary example files.

<!--toc:start-->
- [QEMU ARM Machine Project](#qemu-arm-machine-project)
  - [Contents](#contents)
  - [Prerequisites](#prerequisites)
  - [Tutorials I Followed](#tutorials-i-followed)
  - [Usage](#usage)
    - [Generating SD Card Image](#generating-sd-card-image)
    - [Mounting the SD Card Image](#mounting-the-sd-card-image)
    - [Unmounting the SD Card Image](#unmounting-the-sd-card-image)
    - [Booting the QEMU Machine](#booting-the-qemu-machine)
  - [Example Files](#example-files)
  - [Versions](#versions)
  - [License](#license)
<!--toc:end-->

## Contents

- `machine`: Bash script to manage the QEMU ARM machine.
- `examples/`: Directory containing example files for the `boot` and `root`
filesystem.

## Prerequisites

- QEMU

- Parted

- Loopback device setup utilities (usually part of util-linux)

## Tutorials I Followed

- [Qemu (Raspberrypi at Home) Embedded Linux Playlist by Moatasem Elsayed](https://youtube.com/playlist?list=PLkH1REggdbJpM0RpqpLjr_LrXoezYCGch&si=Q8vx4YyWtZrYQoQg)

- [bootlin's Embedded Linux System Development - QEMU ARM variant - Practical Labs](https://bootlin.com/doc/training/embedded-linux-qemu)

## Usage

The script will prompt for sudo when necessary.

### Generating SD Card Image

To create a new SD card image:

```sh
./machine generate
```

This will create an empty 1GB image `sdcard.img`, partition it, 
format the partitions and copy the example files to the appropriate partitions.

**Partitions:**

| Name   | Size     | Type  | MountPoint |
| :----: | :------: | :---: | :--------- |
| Boot   | 10MiB    | FAT16 | /boot      |
| RootFS | The rest | ext4  | /          |

After generating the image, you need to:

- Add the `uboot` file to the same working directory (`./uboot`).

- Copy the `zImage` file to `mnt/root/`:

```sh
# The path could be different in your case
sudo cp -f zImage mnt/root/
```

- Copy the `crosstool-ng sysroot dir` to `mnt/rootfs/`:

```sh
# The path could be different in your case
sudo cp -rf ~/x-tools/arm-linux-gnueabihf/arm-linux-gnueabihf/sysroot/* mnt/rootfs/
```

- Copy the `busybox output` to `mnt/rootfs/` too:

```sh
# The path could be different in your case
sudo cp -rf busybox/_install/* mnt/rootfs/
```

> Don't forget to unmount the mnt:

```sh
./machine umount
```

### Mounting the SD Card Imager

To mount the SD card image:

```sh
./machine mount
```

This will mount the `boot` and `root` filesystem partitions at `./mnt/boot`
and `mnt/rootfs` respectively.

### Unmounting the SD Card Image

To unmount the SD card image:

```sh
./machine umount
```

This will unmount the boot and root filesystem partitions
and detach the loop device.

### Booting the QEMU Machine

To boot the QEMU machine:

```sh
./machine boot
```

This will start the QEMU ARM machine with the SD card image.

## Example Files

Path `examples/`: Directory containing example files for the boot and root
filesystem.

- **examples/boot/uboot.env**: U-Boot environment file.

    The following environment variables are set:

    - **bootargs:** `console=ttyAMA0,115200 root=/dev/mmcblk0p2 rootfstype=ext4 rw`

    - **bootcmd:** `fatload mmc 0:1 $kernel_addr_r zImage; fatload mmc 0:1 $fdt_addr_r vexpress-v2p-ca9.dtb; bootz $kernel_addr_r - $fdt_addr_r`

- **examples/boot/vexpress-v2p-ca9.dtb**: Device Tree file of the **vexpress-cortext9**.

- **examples/rootfs/etc/**: Some configuration files and init scripts including:
    passwd, shadow, group, fstab, inittab, issue, hostname

## Versions

- **zImage** and **vexpress-v2p-ca9.dtb**: Linux Kernel 6.7.y

- **Crosstool-ng**: 1.26.0

- **Busybox**: 1.36.1

- **U-Boot**: 2023.01

## License

This project is licensed under the MIT License.
