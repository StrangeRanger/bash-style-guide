The code below is from an installer project that can be referred to at this link: https://github.com/StrangeRanger/NadekoBot-BashScript/blob/master/linuxAIO.sh

## Example

```bash
#!/bin/bash
#
# 'linuxAIO.sh' acts as the intermediary between the system NadekoBot is being hosted on
# and 'installer_prep.sh'. To prevent any conflicts with updates to the installer, this
# script has as little code as deemed necessary.
#
# README: Because this script remains on the user's system, any changes to the code that
#         are pushed to github, are never applied to the version on the user's system.
#         To get around this, the variable $_LINUXAIO_REVISION contains a revision
#         number that is updated every time any changes are made to 'linuxAIO.sh'.
#         Another variable in 'installer_prep.sh' ($current_linuxAIO_revision) gets
#         updated alongside with $_LINUXAIO_REVISION. Whenever the user executes the
#         installer, 'installer_prep.sh' will compare the two variables. If they are not
#         of equal value, the newest version of 'linuxAIO.sh' is retrieved from github.
#
########################################################################################
####[ Variables ]#######################################################################


####[[ Configuration Variables ]]#######################################################
# Variables used to modify the behavior of the installer.
#
# ~~~ THESE VARIABLES CAN BE MODIFIED BY THE END-USER ~~~
#
# Whenever the installer retrieves the newest version of 'linuxAIO.sh', all modified
# variables, with the exception of $installer_repo and $_FILES_TO_BACK_UP, will be
# applied to the new version of this script.


# The repository containing all of the scripts used by the installer.
#
# The only time that this variable should be modified, is if you have created a fork of
# the repo and plan on making your own modifications to the installer.
#
# Format: installer_repo="[github username]/[repository name]"
installer_repo="StrangeRanger/NadekoBot-BashScript"

# The branch of $installer_repo that the installer will download its scripts from.
#
# Options:
#   main     = Production ready code (the latest stable code)
#   NadekoV3 = The version of the installer designed for NadekoBot v3
#   dev      = Non-production ready code (has the possibility of breaking something)
#
# Default: "main"
installer_branch="main"

# The branch/tag, of NadekoBot's official repo, that the installer will download the bot
# from.
#
# IMPORTANT: Using a branch/tag containing code older than the one currently on your
#            system, increases the likelihood of failed builds due to incompatible
#            changes in the code/files coppied from the current to the newly downloaded
#            version. For this, and other reasons, it's generally not recommended to
#            to modify $_NADEKO_INSTALL_VERSION. This is especially true when it comes
#            to a difference of major version changes, such as v3 and v4.
#
# Options:
#   v4    = Latest version (the master/main branch)
#   v3    = NadekoBot v3
#   x.x.x = Any other branch/tag (refer to the NadekoBot repo for available tags and
#           branches)
#
# Default: "v4"
export _NADEKO_INSTALL_VERSION="v4"

# A list of files to be backed up when executing option 7.
#
# When adding a new file to the variable below, make sure to follow these rules:
#   1. The path, starting at the project's parent directory, to the file must always be
#      included. This means that unless modified by the end-user, the beginning of the
#      path will start with 'nadekobot/', followed by the rest of the path to the file.
#   2. Each file must be seperated by a single space or placed on its own line.
#       - Valid:   "nadekobot/output/creds.yml
#                   nadekobot/output/data/bot.yml"
#       - Valid:   "nadekobot/output/creds.yml nadekobot/output/data/bot.yml"
#       - Invalid: "nadekobot/output/creds.yml, nadekobot/output/data/bot.yml"
#       - Invalid: "nadekobot/output/creds.yml,nadekobot/output/data/bot.yml"
#   3. Niether the file nor the path to the file can contain a space.
#      - Valid:   'nadekobot/output/data/NadekoBot.db'
#      - Invalid: 'nadeko bot/output/data/NadekoBot.db'
#
# Default: "nadekobot/output/creds.yml
#   nadekobot/output/data/NadekoBot.db
#   nadekobot/output/data/bot.yml
#   nadekobot/output/data/gambling.yml
#   nadekobot/output/data/games.yml
#   nadekobot/output/data/images.yml
#   nadekobot/output/data/xp.yml
#   nadekobot/output/data/xp_template.json"
export _FILES_TO_BACK_UP="nadekobot/output/creds.yml
nadekobot/output/data/NadekoBot.db
nadekobot/output/data/bot.yml
nadekobot/output/data/gambling.yml
nadekobot/output/data/games.yml
nadekobot/output/data/images.yml
nadekobot/output/data/xp.yml
nadekobot/output/data/xp_template.json"


####[[ General Variables ]]#############################################################


# Revision number of this script.
# Refer to the 'README' note at the beginning of this file for more information.
export _LINUXAIO_REVISION=36
# The URL to the raw code of a script that is specified by the other scripts.
export _RAW_URL="https://raw.githubusercontent.com/$installer_repo/$installer_branch"


####[ Main ]############################################################################


echo "Downloading the latest installer..."
curl -O "$_RAW_URL"/installer_prep.sh
sudo chmod +x installer_prep.sh && ./installer_prep.sh
exit "$?"  # Uses the exit code passed by 'installer_prep.sh'.
```
