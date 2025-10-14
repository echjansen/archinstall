# archinstall
Installing Arch Linux using the live ISO provided 'archinstall' package

Used with `archinstall`'s \*guided\* installation script. See \[Guided Installation](https://archinstall.archlinux.page/installing/guided.html#guided-installation) for detailed explanation of how to make and use configuration files.

## How to create the  configuration files

1. In Archlinux, install the `archinstall` package:

&nbsp;   ```bash

&nbsp;   sudo pacman -S archinstall

&nbsp;   ```

2. Run `archinstall` \*with root privileges\* and \*with `--dry-run` option\*(in safe mode).

&nbsp;   ```bash

&nbsp;   sudo archinstall --dry-run

&nbsp;   ```

3. Set installation options to your preferences.

4. `Save configuration` to your location.


## Usage (with configuration files)

1. Boot with Archlinux ISO Image.

2. Prepare configuration files. You could use them from previous steps, but since I uploaded them here, I'd download them via `curl`(FYI: Archlinux ISO image does \*not\* contain `wget` or `git`), e.g.:

&nbsp;   ```bash
&nbsp;   curl -O https://raw.githubusercontent.com/echjansen/archinstall/main/user_configuration.json
&nbsp;   curl -O https://raw.githubusercontent.com/echjansen/archinstall/main/user_credentials.json
&nbsp;   ```

&nbsp;   Modify the downloaded files to your preferences.

3. Run `archinstall` with `--config` and `--creds` options (with each configiration file as argument, such as `user\_configuration.json` and `user\_credentials.json`).

&nbsp;   ```bash

&nbsp;   archinstall --config user\_configuration.json --creds user\_credentials.json

&nbsp;   ```

&nbsp;   You can pass \*\*remote URL\*\* instead of file path:

&nbsp;   ```bash

&nbsp;   archinstall --config <your\_link\_to\_configuration\_file> --creds <your\_link\_to\_credentials\_file>

&nbsp;   ```

4. Confirm loaded configurations and proceed to installation if all seems to be ready.
