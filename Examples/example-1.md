The code below is from an installer project that can be refered to at this link: https://github.com/StrangeRanger/NadekoBot-BashScript/blob/master/linuxAIO.sh

## Example

```bash
#!/bin/bash
#
# 'linuxAIO.sh' acts as the intermediary between the system NadekoBot is being hosted on
# and 'installer_prep.sh'. To prevent any conflicts with updates to the installer, this
# script has as little code as deemed necessary.
#
########################################################################################
#### [ Variables ]

########################################################################################
#### [[ Configuration Variables ]]
#### Variables used to modify the behavior of the installer.
####
#### ~~~ THESE VARIABLES CAN BE MODIFIED BY THE END-USER ~~~
####
#### Whenever the installer retrieves the newest version of 'linuxAIO.sh', all modified
#### variables, with the exception of $installer_repo, will be applied to the new
#### version of this script.


# The repository that the installer will use.
#
# The only time that this variable should be modified, is if you have created a fork of
# the repo and plan on making your own modifications to the installer.
#
# Format: installer_repo="[github username]/[repository name]"
installer_repo="StrangeRanger/NadekoBot-BashScript"

# The branch that the installer will use. 
#
# Options
# -------
# master = Production ready code (the latest stable code)
# dev    = Non-production ready code (has the possibility of breaking something)
#
# Default: master
installer_branch="master"

# Determines whether or not the installer can be run as the root user.
#
# Options
# -------
# true  = can be run with root privilege
# false = cannot be run with root privilege (recommended)
#
# Default: false
allow_run_as_root=false

# The branch or tag that the installer will download NadekoBot from.
#
# Options
# -------
# 1.9   = Latest version (the master/main branch)
# x.x.x = Any other branch/tag (refer to the NadekoBot repo for available tags and
#         branches)
#
# Default: 1.9
export _NADEKO_INSTALL_VERSION="1.9"


#### End of [[ Configuration Variables ]]
########################################################################################
#### [[ General Variables ]]


# Used to keep track of changes to 'linuxAIO.sh'.
# Refer to the '[ Prepping ]' section of 'installer_prep.sh' for more information.
export _LINUXAIO_REVISION="17"
# URL to the raw version of a specified script.
export _RAW_URL="https://raw.githubusercontent.com/$installer_repo/$installer_branch"


#### End of [[ General Variables ]]
########################################################################################

#### End of [ Variables ]
########################################################################################
#### [ Main ]


# If executed with root privilege and $allow_run_as_root is false...
if [[ $EUID = 0 && $allow_run_as_root = false ]]; then
    echo "\033[1;31mPlease run this script without root privilege" >&2
    echo "\033[0;36mWhile you will be performing specific tasks with root privilege," \
        "running the installer in it's entirety as root is not recommended\033[0m"
    echo -e "\nExiting..."
    exit 1
fi

echo "Downloading the latest installer..."
curl -O "$_RAW_URL"/installer_prep.sh
sudo chmod +x installer_prep.sh
./installer_prep.sh


#### End of [ Main ]
########################################################################################
```
