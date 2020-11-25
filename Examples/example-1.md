The code below is from an installer project that can be refered to at this link: https://github.com/StrangeRanger/NadekoBot-BashScript/blob/master/linuxAIO.sh

## Example

```bash
#!/bin/bash

################################################################################
#
# linuxAIO acts as the intermediary between the system NadekoBot is being hosted
# on and the 'installer_prep.sh'. To prevent any conflicts with updates to
# the installer, this script has as little code as deemed necessary.
#
################################################################################
#
# [ Development variables ]
#
# The variables below are for dev/testing purpouses (DO NOT MODIFY).
#
################################################################################
#
    export linuxAIO_revision="5"                                # Keeps track of changes to linuxAIO.sh
    export installer_repo="StrangeRanger/NadekoBot-BashScript"  # Determines which repo is used

#
################################################################################
#
# [ Configuration varaibles ]
#
# The variables below are used to configure the installer in one way or another,
# and CAN BE modified by the end user.
#
################################################################################
#
    # Determines from which branch from the installer repo will be used
    # release/latest = The most recent release
    # master         = The latest stable code
    # dev            = Non-production ready code (may break your system)
    # 
    # Default: release/latest
    export installer_branch="release/latest"

    # Determines whether or not the installer can be run as the root user:
    # true = can be run with root privilege
    # false = cannot be run with root privilege (recommended)
    #
    # Default: false
    allow_run_as_root=false

#
################################################################################
#
# [ Main ]
#
################################################################################
#
    # Checks if the script was executed with root privilege
    if ((EUID == 0)) && [[ $allow_run_as_root = false ]]; then
        echo "\033[1;31mPlease run this script without root privilege" >&2
        echo "\033[0;36mWhile you will be performing specific tasks with root" \
            "priviledge running the installer in it's entirety as root is not" \
            "recommended\033[0m"
        echo -e "\nExiting..."
        exit 1
    fi

    echo "Downloading the latest installer..."
    curl https://raw.githubusercontent.com/"$installer_repo"/"$installer_branch"/installer_prep.sh \
            -o installer_prep.sh || {
        echo "Failed to download 'installer_prep.sh'..." >&2
        echo -e "\nExiting..."
        exit 1
    }
    sudo chmod +x installer_prep.sh && ./installer_prep.sh
```
