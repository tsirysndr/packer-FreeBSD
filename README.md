packer-FreeBSD
==============

This repository contains the necessary tools to build a Qemu
FreeBSD virtual machine using Packer.

> [!NOTE]
> There are [official FreeBSD] VMs available from the Vagrant Cloud.

Prerequisites
--------------

- [Packer]

- [QEMU]

Instructions
------------

To create a box:

1.  Clone this repository:

        $ git clone https://github.com/tsirysndr/packer-FreeBSD.git
        $ cd packer-FreeBSD

2.  Initialize packer:

        $ packer init .

3.  Build the box:

        $ packer build -only=qemu.freebsd .


------------------------------------------------------------------------

### Build Options

Below is a sample `variables.pkrvars.hcl` file:

```hcl
arch            = "amd64"
branch          = "RELEASE"
build_date      = ""
cpus            = 1
directory       = "releases"
disk_size       = 10240
filesystem      = "zfs"
zfs_compression = "zstd"
git_commit      = ""
guest_os_type   = "FreeBSD_64"
memory          = 1024
mirror          = "https://download.freebsd.org"
rc_conf_file    = ""
revision        = "14.0"
```

The following variables can be set:

-   `cpus` is the number of CPUs assigned.  _Default:_ `1`

-   `disk_size` is the HDD size in megabytes.  _Default:_ `10240`

-   `memory` is the amount of RAM in megabytes assigned.  _Default:_
    `1024`

-   `revision` is the FreeBSD revision number.  _Default:_ `14.0`

-   `branch` used in conjunction with `build_date`, `git_commit` and
    `directory`.  _Default:_ `RELEASE`

    See FreeBSD's [Release Branches] for more information.  Possible
    values are:

    | Branch                | Directory   |
    | ------                | ---------   |
    | `CURRENT`             | `snapshots` |
    | `STABLE`              | `snapshots` |
    | `ALPHA1`, `ALPHA2`, … | `snapshots` |
    | `PRERELEASE`          | `snapshots` |
    | `BETA1`, `BETA2`, …   | `releases`  |
    | `RC1`, `RC2`, …       | `releases`  |
    | `RELEASE`             | `releases`  |

-   `arch` is the target architecture (`i386` or `amd64`).  _Default:_
    `amd64`

-   `guest_os_type` (VirtualBox) used in conjunction with `arch`
    (`FreeBSD` or `FreeBSD_64`).  See [packer's
    documentation](https://www.packer.io/docs/builders/virtualbox-iso.html#guest_os_type).
    _Default:_ `FreeBSD_64`

-   `filesystem` is the file system type (`ufs` or `zfs`).  _Default:_
    `zfs`

-   `zfs_compression` is the ZFS compression algorithm (`zstd` or `lz4`).
    _Default:_ `zstd`

-   `mirror` is the preferred FreeBSD mirror.  _Default:_
    `https://download.freebsd.org`

-   `rc_conf_file` is the file where `rc.conf` parameters are stored.
    _Default: empty_ .  Possible values are:

    | Value    | File                                          |
    | -----    | ----                                          |
    |          | `/etc/rc.conf`                                |
    | `local`  | `/etc/rc.conf.local` (Its use is discouraged) |
    | `vendor` | `/etc/defaults/vendor.conf`                   |
    | `name`   | `(/usr/local)/etc/rc.conf.d/<name>`           |

Create a `variables.pkrvars.hcl` file overriding the default
values, and invoke:

    $ packer build -var-file="variables.pkrvars.hcl" .

You can also select which components you wish to install.  By default,
it runs the following provisioning scripts:

| Name         | Description                                                               |
| ----         | -----------                                                               |
| [`update`]   | Updates to the latest patch level (if applicable) and the latest packages |
| [`vagrant`]  | Vagrant-related configuration                                             |
| [`zeroconf`] | Enables zero-configuration networking                                     |
| [`ansible`]  | Installs python and CA Root certificates                                  |
| [`vmtools`]  | Virtual Machine-specific utilities                                        |
| [`cleanup`]  | Cleanup script (must be called last)                                      |

The following scripts are also available:

| Name           | Description                      |
| ----           | -----------                      |
| [`cloud-init`] | Installs cloud-init              |
| [`hardening`]  | Provides basic hardening options |
| [`ports`]      | Installs the FreeBSD ports tree  |

### Handling `.iso` and `.box` files

Packer will automatically download the `.iso` image if it does not find
the right one under the `iso` directory.  Optionally, you can download
the `.iso` image and save it to the `iso` directory.

`.box` files will be created under the `builds` directory.

[official FreeBSD]: https://app.vagrantup.com/freebsd
[Release Branches]: https://docs.freebsd.org/en/books/dev-model/#release-branches
[Packer]: https://developer.hashicorp.com/packer/downloads#install
[QEMU]: https://www.qemu.org/download/
[Vagrant]: https://developer.hashicorp.com/vagrant/downloads
[VirtualBox]: https://www.virtualbox.org/wiki/Downloads
[VMWare Fusion]: https://www.vmware.com/products/fusion.html
[`ansible`]: https://github.com/tsirysndr/packer-FreeBSD/blob/main/scripts/ansible.sh
[`cleanup`]: https://github.com/tsirysndr/packer-FreeBSD/blob/main/scripts/cleanup.sh
[`cloud-init`]: https://github.com/tsirysndr/packer-FreeBSD/blob/main/scripts/cloud-init.sh
[`hardening`]: https://github.com/tsirysndr/packer-FreeBSD/blob/main/scripts/hardening.sh
[`ports`]: https://github.com/tsirysndr/packer-FreeBSD/blob/main/scripts/ports.sh
[`update`]: https://github.com/tsirysndr/packer-FreeBSD/blob/main/scripts/update.sh
[`vmtools`]: https://github.com/tsirysndr/packer-FreeBSD/blob/main/scripts/vmtools.sh
[`zeroconf`]: https://github.com/tsirysndr/packer-FreeBSD/blob/main/scripts/zeroconf.sh
