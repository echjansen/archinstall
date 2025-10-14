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
#### Change the pasword (hash)

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

```json

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
