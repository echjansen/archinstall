# archinstall
Installing Arch Linux using the live ISO provided 'archinstall' package

```

> [!NOTE]
> This section of the guide deals with installing the base system, setting up timezones, locale, hostname, hosts, creating new non-root user's, setting passwords for both `root` and `non-root` user accounts.
> This is generally user specific configuration, and you might have a different setup you might, want to follow.
> So it is recommended to refer to official [Arch Wiki Installation guide](https://wiki.archlinux.org/title/installation_guide#Installation), for this section. And you may come back here and follow from the next section, when it is time to [configure mkinitcpio](https://github.com/joelmathewthomas/archinstall-luks2-lvm2-secureboot-tpm2#7-configure-mkinitcpio).

```

> [!TIP]
> If a logical volume will be formatted with ext4, leave at least 256 MiB free space in the volume group to allow using `e2scrub`. After creating the last volume with `-l 100%FREE`, this can be accomplished by reducing its size with `lvreduce -L -256M MyVolGroup/home`.

```

> [!WARNING]
> This will erase all data on the disk. Ensure you have selected the correct device.
