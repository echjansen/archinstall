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

> [!TIP]
> Remove the line(s) in 'device_modifications' specifying the disk/device to use: "device": "/dev/sda".
> Doing so will force 'archinstall' to ask the user what drive to use during installation.


> [!TIP]
> Remove all lines specifiying 'dev_path':
>   "dev_path": null,
> This will cause the installer to ask the user to specify the partition to use during installation.



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
