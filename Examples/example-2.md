The code below is from an installer project that can be refered to at this link: https://github.com/StrangeRanger/NadekoBot-BashScript/blob/master/linuxAIO.sh

The code is similar to the code in example-1.md, but from a different project, and older.

## Example

``` bash
#!/bin/bash

################################################################################
#
# linuxPMI acts as the intermediary between the server Botler is being hosted
# on and the linux master installer. To prevent any conflict with updates to
# the installer, this script has as little code as deemed necessary. In
# addition, linuxPMI is the only script that will remain on the system.
#
################################################################################
#
    export linuxPMI_revision="4"    # Keeps track of changes to linuxPMI.sh
    export botler_version="latest"  # Determins which version of Botler is used
    export installer_branch="dev"   # Determins which installer branch is used

    # Checks to see if this script was executed with root privilege
    if ((EUID != 0)); then 
        echo "Please run this script as root or with root privilege" >&2
        echo -e "\nExiting..."
        exit 1 
    fi

    echo "Downloading 'installer-prep.sh'..."
    wget -N https://raw.githubusercontent.com/Botler-Dev/Installer/"$installer_branch"/installer-prep.sh || {
        echo "Failed to download 'installer-prep.sh'..." >&2
        echo -e "\nExiting..."
        exit 1
    }
    chmod +x installer-prep.sh && ./installer-prep.sh
```
