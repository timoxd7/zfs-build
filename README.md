# ZFS Build

This is my small documentation how i built ZFS for my Armbian-based OMV Install.

## Pre-Build changes

See the original fix in `zfs-2.3.0-omv` for some changes. As the native-deb builds of zfs use an openzfs prefix, this will conflict with the openmediavault-zfs package. To fix this, the original names were added as provides section, which fixes it.

## Dependencies

See [this](https://openzfs.github.io/openzfs-docs/Developer%20Resources/Building%20ZFS.html) for dependencies needed for zfs building.

## Building

Don't forget `git clean -dxff` to forget the past.

Steps:

```sh
sh autogen.sh && ./configure --enable-systemd && make -s -j$(nproc) && make native-deb
```

Now you have the .dep packages in the parent directory.

## Pre-Installation

Remove every zfs package currently in your system, so zfs, zfs-dkms, zfsutils, libzpool, libzfs... and also remove the openmediavault-zfs package. You might do it with a purge to remove it fully.

IMPORTANT: You need the linux headers for the currently running kernel. If you just did an update, which might updated the kernel, reboot the system to use the new kernel. Use armbian-config to install the correct version of the headers.

## Installation

This is based off 2.3.0, so update the version accordingly. Go into the parent folder of the git repo, there the built files are located.

```sh
## Install zfs
sudo apt install ./openzfs-zfs-dkms_2.3.0-1_all.deb ./openzfs-zfsutils_2.3.0-1_arm64.deb ./openzfs-zfs-zed_2.3.0-1_arm64.deb ./openzfs-libnvpair3_2.3.0-1_arm64.deb ./openzfs-libuutil3_2.3.0-1_arm64.deb ./openzfs-libzfs6_2.3.0-1_arm64.deb ./openzfs-libzpool6_2.3.0-1_arm64.deb

## Install omv integration
sudo apt install openmediavault-zfs
```

## Post-Install

After installing, you might want to set the packages as automatically installed to be removed if you remove openmediavault-zfs. To do this, use:

```sh
sudo apt-mark auto openzfs-zfs-dkms openzfs-zfsutils openzfs-zfs-zed openzfs-libnvpair3 openzfs-libuutil3 openzfs-libzfs6 openzfs-libzpool6
```

To list packages with a given mark, use the following:

```sh
sudo apt-mark showauto
sudo apt-mark showhold # Packages on hold, not to install from repositories
sudo apt-mark showmanual # These will only be removed if explicitly done via command
```

## Kernel-Updates

apt might update your kernel. After this, zfs _might_ fail to be loaded. To fix, ensure linux headers for the new kernel are installed, maybe reboot and do the following:

```sh
sudo dkms autoinstall
```

This will rebuild and install the zfs module.
