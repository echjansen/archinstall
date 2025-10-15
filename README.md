# Archinstall
Installing Arch Linux using the live ISO provided 'archinstall' package

> [!NOTE]
> These instructions and examples asume you are using version 3.0 of the 'archinstall' application.

Used with `archinstall`'s *guided* installation script. See [Guided Installation](https://archinstall.archlinux.page/installing/guided.html#guided-installation) for detailed explanation of how to make and use configuration files.

## How to create the 'base' configuration files

1. In an existing Archlinux system, install the `archinstall` package:

```bash
sudo pacman -S archinstall --advanced --verbose --offline --dry-run
```

2. Run `archinstall` *with root privileges* and *with `--dry-run` option*(in safe mode).

```bash
sudo archinstall --dry-run
```

3. Set the installation options to your preferences.

4. `Save configuration` to a directory of your choice.

> [!TIP]
> 'Save configuration' will ask for a directory to save the configuration files. This is more complicated when using the 'archinstall' on a live ISO as there is no mounted directories to save the configuration to. Running 'Save configuration' on an already installed Arch Linux operating system allows you to save in any existing (persistent) directory. Do note you will likely need to change the permission of the saved files with 'chown'.

## Modifying the 'base' configuration files.

### user_credentials.json

> [!CAUTION]
> The user_crendetial.json file will save your disk encryption password in plain text.
> There is no possibility to hash the LUKS password in the user_credential.json file.
> Therefor it is best to change the 'encryption_password' to an empty string (""). This will cause 'archinstll' to prompt you for a LUKS password during installation.

#### Example of user_credentials.json:
```json
{
    "encryption_password": "",
    "root_enc_password": "$y$j9T$KjiKr/CTRnfZtPi363mck0$HXzYkWNXAcMBmwbENu/JNmrHV.yWqErGn74dhKlffk8",
    "users": [
        {
            "enc_password": "$y$j9T$wv9tyZmU4aYBVgkAvvtyS.$w.t2UVFnvhiA2gT9C7pplcF2B.tHz18UzI5TGq1BGtA",
            "groups": [],
            "sudo": true,
            "username": "linus"
        }
    ]
}
```
#### Changing the pasword (hash)

If you wish to change the password for root or any of the users for future installations you can manually create the hashed passwords with one of the following commands:

1. On the live ISO (or install from the AUR):
```bash
mkpasswd
```

2. On a Arch Linux system:
```bash
sudo pacman -S pipx
pipx install shadowhash
shadowhash <PASSWORD>
```

### user_configuration.json

The user_configuration.json file saved during the first step is a representation of that specific installation.
Some of the saved configurations you might want to change if you wish to use the user_configuartion.json for future new installations.

#### Disk configuration

Likely the most interesting part of the user_configuration.json file is the disk configuration.
Each system that you install Arch Linux on will likely have a different 'disk' configuration.

Here are some recommendations on how the confiure the 'disk' and to create a more 'useable' version:

Example section for 'disk_config' direct from a 'saved' configuration:
```json
    "disk_config": {
        "btrfs_options": {
            "snapshot_config": {
                "type": "Timeshift"
            }
        },
        "config_type": "default_layout",
        "device_modifications": [
            {
                "device": "/dev/sda",
                "partitions": [
                    {
                        "btrfs": [],
                        "dev_path": null,
                        "flags": [
                            "boot",
                            "esp"
                        ],
                        "fs_type": "fat32",
                        "mount_options": [],
                        "mountpoint": "/boot",
                        "obj_id": "f8b6965c-5629-4696-a95e-c1cbede3bd85",
                        "size": {
                            "sector_size": {
                                "unit": "B",
                                "value": 512
                            },
                            "unit": "GiB",
                            "value": 1
                        },
                        "start": {
                            "sector_size": {
                                "unit": "B",
                                "value": 512
                            },
                            "unit": "MiB",
                            "value": 1
                        },
                        "status": "create",
                        "type": "primary"
                    },
                    {
                        "btrfs": [
                            {
                                "mountpoint": "/",
                                "name": "@"
                            },
                            {
                                "mountpoint": "/home",
                                "name": "@home"
                            },
                            {
                                "mountpoint": "/var/log",
                                "name": "@log"
                            },
                            {
                                "mountpoint": "/var/cache/pacman/pkg",
                                "name": "@pkg"
                            }
                        ],
                        "dev_path": null,
                        "flags": [],
                        "fs_type": "btrfs",
                        "mount_options": [
                            "compress=zstd"
                        ],
                        "mountpoint": null,
                        "obj_id": "909fc80e-25c2-4310-9403-097f4619d9d4",
                        "size": {
                            "sector_size": {
                                "unit": "B",
                                "value": 512
                            },
                            "unit": "B",
                            "value": 159985434624
                        },
                        "start": {
                            "sector_size": {
                                "unit": "B",
                                "value": 512
                            },
                            "unit": "B",
                            "value": 1074790400
                        },
                        "status": "create",
                        "type": "primary"
                    }
                ],
                "wipe": true
            }
        ],
        "disk_encryption": {
            "encryption_type": "luks",
            "lvm_volumes": [],
            "partitions": [
                "909fc80e-25c2-4310-9403-097f4619d9d4"
            ]
        }
    },
```

#### "disk_config.device_modifications.device"
> [!TIP]
> Remove the line(s) in 'device_modifications' specifying the disk/device to use: "device": "/dev/sda".
> Doing so will force 'archinstall' to ask the user what device / drive to use during the installation process.

#### "disk_config.device_modifications.partitions.dev_path"
> [!TIP]
> Remove the line(s) in "disk_config.device_modifications.partitions" specifying "dev_path".
> This will cause the installer to create partitions in order of ocurance in the user_configuration.json file.

#### "disk_config.device_modifications.partitons.obj_id"
> [!TIP]
> Remove the line(s) in "disk_config.device_modifications.partitions" specifying "obj_id".
> This will indicate 'archinstall' that it is a new installation, and the partition(s) will need to be created newly.

#### "disk_config.device_modifications.partitions.size"
> [!TIP]
> Simplify and remove clutter surrounding the partitions size configuration.
> Specify the size with "size": { "unit": "MiB", "value": 512 }
> Specify the remaining size with "size": { "unit": "B", "value": 0 }, where 0 results in remaining disk capacity.

Example of sizing EFI and ROOT partitions:
```json
"device_modifications": [
    {
        "partitions": [
            {
                // Partition 0: EFI/boot (512 MiB)
                "flags": [
                    "boot",
                    "esp"
                ],
                "fs_type": "fat32",
                "mountpoint": "/boot",
                "size": {
                    "unit": "MiB",
                    "value": 512
                },
                "start": {
                    "unit": "MiB",
                    "value": 1
                },
                "status": "create",
                "type": "primary"
            },
            {
                // Partition 1: BTRFS (Remaining Space)
                "btrfs": [
                    { "mountpoint": "/", "name": "@" },
                    { "mountpoint": "/home", "name": "@home" }
                    // Add any other subvolumes you need here
                ],
                "flags": [],
                "fs_type": "btrfs",
                "mount_options": [
                    "compress=zstd"
                ],
                "mountpoint": null,
                "size": {
                    "unit": "B",
                    "value": 0 // <-- This is the key: 0 uses all remaining disk space
                },
                "status": "create",
                "type": "primary"
            }
        ],
        "wipe": true
    }
]
```

#### "disk_config.partitions.btrfs"
The default btrfs scheme used in 'archinstall' is a simplified version of what is generally usefull in Arch Linux.
Below is a configuration taking advantage of the BTRFS capabilities:

```bash
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home
btrfs subvolume create /mnt/@swap
btrfs subvolume create /mnt/@snapshots
btrfs subvolume create /mnt/@home-snapshots
btrfs subvolume create /mnt/@libvirt
btrfs subvolume create /mnt/@docker
btrfs subvolume create /mnt/@cache-pacman-pkgs
btrfs subvolume create /mnt/@var
btrfs subvolume create /mnt/@var-log
btrfs subvolume create /mnt/@var-tmp
```

Which translates in user_configuration.json to:

```json
"btrfs": [
    { "mountpoint": "/", "name": "@" },
    { "mountpoint": "/home", "name": "@home" }
    { "mountpoint": "/.swap", "name": "@swap" }
    { "mountpoint": "/.snapshots", "name": "@snapshots" }
    { "mountpoint": "/home/.snapshots", "name": "@home-snapshots" }
    { "mountpoint": "/var", "name": "@var" }
    { "mountpoint": "/var/tmp", "name": "@var-tmp" }
    { "mountpoint": "/var/log", "name": "@hvar-log" }
    { "mountpoint": "/var/lib/libvirt", "name": "@libvirt" }
    { "mountpoint": "/var/lib/docker", "name": "@docker" }
    { "mountpoint": "/var/cache/pacman/pkgs", "name": "@cache-pacman-pkg" }
],

```

#### "disk_config.disk_encryption.partitions"
Because 'disk_id' are removed from the user_configuration, the partitions that require to be encrypted need to be chnaged too.
partitions are created in order as they occur in the user_configuration file, starting with 0 for the fist partition.
Therefor change the "partitons" to the partition number instead of the disk_id.

```json
"device_modifications": [
    {
        // NO "device": "/dev/sda" ENTRY HERE
        "partitions": [
            {
                // Partition 0 (EFI)
                // ... partition details ...
            },
            {
                // Partition 1 (BTRFS/LUKS)
                // ... partition details ...
            }
        ],
        "wipe": true
    }
],
"disk_encryption": {
    "encryption_type": "luks",
    "lvm_volumes": [],
    "partitions": [
        "1" // Refers to Partition 1 in the list above
    ]
}
```


## Usage (with configuration files)

1. Boot with Archlinux ISO Image.

2. Prepare configuration files. You could use them from previous steps, but since I uploaded them here, I'd download them via `curl`(FYI: Archlinux ISO image does *not* contain `wget` or `git`), e.g.:

```bash
curl -O https://raw.githubusercontent.com/echjansen/archinstall/main/user_configuration.json
curl -O https://raw.githubusercontent.com/echjansen/archinstall/main/user_credentials.json
```

Modify the downloaded files to your preferences.

3. Run `archinstall` with `--config` and `--creds` options (with each configiration file as argument, such as `user_configuration.json` and `user_credentials.json`).

```bash
archinstall --config user_configuration.json --creds user_credentials.json
```

You can pass **remote URL** instead of file path:

```bash
archinstall --config <your_link_to_configuration_file> --creds <your_link_to_credentials_file>
```

4. Confirm loaded configurations and proceed to installation if all seems to be ready.

> [!TIP]
> If you have checked the files prior, you can start the installation process immediately with the following command:
> Ensure though that the supplied configuration is correct by checking it at least once.

```bash
archinstall --silent --config <your_link_to_configuration_file> --creds <your_link_to_credentials_file>
```
