The code below is from an installer project that can be refered to at this link: https://github.com/StrangeRanger/NadekoBot-BashScript/blob/master/installer_prep.sh. As a note, this code is what's executed by the code in the example-1.md file.

## Example

```bash
#!/bin/bash
#
# This script looks at the operating system, architecture, bit type, etc., to determine
# whether or not the system is supported by NadekoBot. Once the system is deemed as
# supported, the master installer will be downloaded and executed.
#
# Comment key for '[letter].[number].'
# ------------------------------------
# A.1. - Forcing 64 bit architecture
# 
########################################################################################
#### [ Exported and/or Globally Used Variables ]


# Used to keep track of changes to 'linuxAIO.sh'.
# Refer to the '[ Prepping ]' section of this script for more information.
current_linuxAIO_revision="17"
# Name of the master installer to be downloaded.
# REASON: I placed the name in a variable just so there are no chances of accidentally
#         misstyping the filename, in addition to it being slighlty shorter.
master_installer="nadeko_master_installer.sh"
# Store process id of 'installer_prep.sh', in case it needs to be manually killed by a
# sub/child script.
export _INSTALLER_PREP_PID=$$

## Modify output text color.
export _YELLOW=$'\033[1;33m'
export _GREEN=$'\033[0;32m'
export _CYAN=$'\033[0;36m'
export _RED=$'\033[1;31m'
export _NC=$'\033[0m'
export _GREY=$'\033[0;90m'
export _CLRLN=$'\r\033[K'

# PURPOSE: The '--no-hostname' flag for 'journalctl' only works with systemd 230 and
#          later. So if systemd is older than 230, $_NO_HOSTNAME will not be created.
if (($(journalctl --version | grep -oP "[0-9]+" | head -1) >= 230)) 2>/dev/null; then
    export _NO_HOSTNAME="--no-hostname"
fi


#### End of [ Exported and/or Globally Used Variables ]
########################################################################################
#### [ Functions ]


detect_sys_info() {
    ####
    # Function Info
    # -------------
    # Identify the operating system, version number, architecture, bit type (32 or 64),
    # etc.
    ####

    ## For Linux.
    if [[ -f /etc/os-release ]]; then
        . /etc/os-release
        _DISTRO="$ID"
        _VER="$VERSION_ID"  # Version: x.x.x...
        _SVER=${_VER//.*/}  # Version: x
        pname="$PRETTY_NAME"
    ## For macOS.
    else
        _DISTRO=$(uname -s)
        if [[ $_DISTRO = "Darwin" ]]; then
            _VER=$(sw_vers -productVersion)  # macOS version: x.x.x
            _SVER=${_VER%.*}                 # macOS version: x.x
            pname="macOS"
        else
            _VER=$(uname -r)
        fi
    fi

    ## Identify bit and architecture type.
    case $(uname -m) in
        x86_64) bits="64"; _ARCH="x64" ;;
        i*86)   bits="32"; _ARCH="x86" ;;
        armv*)  bits="32"; _ARCH="?" ;;
        *)      bits="?";  _ARCH="?" ;;
    esac
}

linuxAIO_update() {
    ####
    # Function Info
    # -------------
    # Download the latest version of 'linuxAIO.sh' if $_LINUXAIO_REVISION and 
    # $current_linuxAIO_revision aren't of equal value.
    #
    # Purpose
    # -------
    # Since 'linuxAIO.sh' remains on the user's system, any changes to the code that are
    # pushed to github, are never applied to the version on the user's system. Whenever
    # the values of $_LINUXAIO_REVISION and $current_linuxAIO_revision do not match, the
    # newest version of 'linuxAIO.sh' is retrieved from github.
    #
    # Important Note For Revisions 8 and Earlier
    # ------------------------------------------
    # As another note, only 'linuxAIO.sh' files with a revision number of 9 or later
    # will utilize this function. Breaking changes occured between revision 8 and 9, and
    # as a result, I've decided that the end-user will be required to manually download
    # the latest version from github. The script will provide the user with the
    # appropriate command to do this, based on the configurations in their current
    # 'linuxAIO.sh'.
    ####

    ## Save the value of $installer_branch, $allow_run_as_root, $_NADEKO_INSTALL_VERSION
    ## specified in 'linuxAIO.sh', to be set in the new 'linuxAIO.sh'.
    ## NOTE: Declaration and intialation is seperated at the recommendation by
    ##       shellcheck.
    local installer_branch
    local allow_run_as_root
    local nadeko_install_version
    installer_branch=$(grep '^installer_branch=.*' linuxAIO.sh)
    allow_run_as_root=$(grep '^allow_run_as_root=.*' linuxAIO.sh)
    nadeko_install_version=$(grep '^export _NADEKO_INSTALL_VERSION=.*' linuxAIO.sh)

    echo "$_YELLOW'linuxAIO.sh' is not up to date$_NC"
    echo "Downloading latest 'linuxAIO.sh'..."
    curl -O "$_RAW_URL"/linuxAIO.sh

    echo "Applying set configurations to 'linuxAIO.sh'..."
    ## Set $installer_branch inside of the new 'linuxAIO.sh'.
    # Sed for linux.
    sed -i "s/^installer_branch=.*/$installer_branch/" linuxAIO.sh ||
        # Sed for macOS.
        sed -i '' "s/^installer_branch=.*/$installer_branch/" linuxAIO.sh
    
    ## Set $allow_run_as_root inside of the new 'linuxAIO.sh'.
    # Sed for linux.
    sed -i "s/^allow_run_as_root=.*/$allow_run_as_root/" linuxAIO.sh ||
        # Sed for macOS.
        sed -i '' "s/^allow_run_as_root=.*/$allow_run_as_root/" linuxAIO.sh

    ## Set $nadeko_install_version inside of the new 'linuxAIO.sh'.
    # Sed for linux.
    sed -i "s/^export _NADEKO_INSTALL_VERSION=.*/$nadeko_install_version/" linuxAIO.sh ||
        # Sed for macOS.
        sed -i '' "s/^export _NADEKO_INSTALL_VERSION=.*/$nadeko_install_version/" linuxAIO.sh
    
    sudo chmod +x linuxAIO.sh
    echo "${_GREEN}Successfully downloaded the newest version of 'linuxAIO.sh'$_NC"
    _CLEAN_EXIT "0" "Exiting" "true"
}

unsupoorted() {
    ####
    # Function Info
    # -------------
    # Provide the end-user with the option to continue, even if their system isn't
    # officially supported.
    ####

    echo "${_RED}Your operating system/Linux Distribution is not OFFICIALLY supported" \
        "for the installation, setup, and/or use of NadekoBot" >&2
    echo "${_YELLOW}WARNING: By continuing, you accept that unexpected behaviors" \
        "may occur. If you run into any errors or problems with the installation and" \
        "use of the NadekoBot, you are on your own. We do not provide support for" \
        "distributions that we don't officially support.$_NC"
    read -rp "Would you like to continue anyways? [y/N] " choice
    # Convert user input to lowercase.
    choice=$(echo "$choice" | tr '[:upper:]' '[:lower:]')
    case "$choice" in
        y|yes) clear -x; execute_master_installer ;;
        n|no)  _CLEAN_EXIT "0" "Exiting" ;;
        *)     _CLEAN_EXIT "0" "Exiting" ;;
    esac
}

execute_master_installer() {
    ####
    # Function Info
    # -------------
    # Download and execute 'nadeko_master_installer.sh'.
    ####

    _DOWNLOAD_SCRIPT "$master_installer" "$master_installer" "true"
    ./nadeko_master_installer.sh
}


########################################################################################
#### [[ Functions To Be Exported ]]


_CLEAN_EXIT() {
    ####
    # Function Info
    # -------------
    # Cleanly exit the installer by removing files that aren't required unless the
    # installer is currently running.
    #
    # @param $1 Exit status code.
    # @param $2 Output text.
    # @param $3 Determines if 'Cleaning up...' needs to be printed with a new-line
    #           symbol.
    ####

    # Files to be removed.
    local installer_files=("credentials_setup.sh" "installer_prep.sh"
        "prereqs_installer.sh" "nadeko_latest_installer.sh" "nadeko_runner.sh"
        "nadeko_master_installer.sh")

    ### PURPOSE: Sometimes the output requires the use of a new-line symbol to seperate 
    ###          the previous text, though sometimes it doesn't.
    if [[ $3 = true ]]; then
        echo "Cleaning up..."
    else
        echo -e "\nCleaning up..."
    fi

    ## Remove any and all files specified in $installer_files.
    for file in "${installer_files[@]}"; do
        if [[ -f $file ]]; then rm "$file"; fi
    done

    echo "$2..."
    exit "$1"
}

_DOWNLOAD_SCRIPT() {
    ####
    # Function Info
    # -------------
    # Download the specified script and modify it's execution permissions.
    #
    # @param $1 Name of script to donwload.
    # @param $2 Name to rename $1 with. 
    ####

    if [[ ! $3 ]]; then echo "Downloading '$1'..."; fi

    curl -s "$_RAW_URL"/"$1" -o "$2"
    sudo chmod +x "$2"
}


#### End of [[ Functions To Be Exported ]]
########################################################################################

#### End of [ Functions ]
########################################################################################
#### [ Error Traps ]


# Execute when the user uses 'Ctrl + Z', 'Ctrl + C', or otherwise forcefully exits the
# installer.
trap 'echo -e "\n\nScript forcefully stopped"
    _CLEAN_EXIT "1" "Exiting" "true"' \
    SIGINT SIGTSTP SIGTERM


#### End of [ Error Traps ]
########################################################################################
#### [ Prepping ]


## If the current 'linuxAIO.sh' revision is number 9 or later...
if [[ $_LINUXAIO_REVISION && $_LINUXAIO_REVISION != "$current_linuxAIO_revision" ]]; then
    linuxAIO_update
## If the current 'linuxAIO.sh' revision is number 8 or earlier...
elif [[ $linuxAIO_revision && $linuxAIO_revision != "$current_linuxAIO_revision" ]]; then
    echo "$_YELLOW'linuxAIO.sh' is not up to date"
    echo "${_CYAN}Due to some breaking changes between revision 8 and 9 you are" \
        "required to manually download the newest version of" \
        "'linuxAIO.sh'. You can do so by executing the following:"
    echo "    mv linuxAIO.sh linuxAIO.sh.old && curl -O" \
        "https://raw.githubusercontent.com/$installer_repo/$installer_branch/linuxAIO.sh" \
        "&& sudo chmod +x linuxAIO.sh$_NC"
    _CLEAN_EXIT "0" "Exiting" "true"
fi

# Change the working directory to the location of the executed scrpt.
cd "$(dirname "$0")" || {
    echo "${_RED}Failed to change working directory" >&2
    echo "${_CYAN}Change your working directory to that of the executed script$_NC"
    _CLEAN_EXIT "1" "Exiting" "true"
}

export _WORKING_DIR="$PWD"
export _INSTALLER_PREP="$_WORKING_DIR/installer_prep.sh"


#### End of [ Prepping ]
########################################################################################
#### [ Main ]


clear -x  # Clear the screen of any text.

detect_sys_info
export _DISTRO _SVER _VER _ARCH
export -f _CLEAN_EXIT _DOWNLOAD_SCRIPT

echo "SYSTEM INFO"
echo "Bit Type: $bits"
echo "Architecture: $_ARCH"
printf "Distro: "
# Use $_DISTRO if $pname is empty.
if [[ -n $pname ]]; then echo "$pname"; else echo "$_DISTRO"; fi
echo "Distro Version: $_VER"
echo ""

### Check if the operating system is supported by NadekoBot and installer.
if [[ $_DISTRO = "ubuntu" ]]; then
    if [[ $bits = 64 ]]; then  # A.1.
        case "$_VER" in
            16.04) execute_master_installer ;;
            18.04) execute_master_installer ;;
            20.04) execute_master_installer ;;
            *)     unsupoorted ;;
        esac
    else
        unsupoorted
    fi
elif [[ $_DISTRO = "debian" ]]; then
    if [[ $bits = 64 ]]; then  # A.1.
        case "$_SVER" in
            9)  execute_master_installer ;;
            10) execute_master_installer ;;
            *)  unsupoorted ;;
        esac
    else
        unsupoorted
    fi
elif [[ $_DISTRO = "linuxmint" ]]; then
    if [[ $bits = 64 ]]; then  # A.1.
        case "$_SVER" in
            18) execute_master_installer ;;
            19) execute_master_installer ;;
            20) execute_master_installer ;;
            *)  unsupoorted ;;
        esac
    else
        unsupoorted
    fi
elif [[ $_DISTRO = "Darwin" ]]; then
    case "$_SVER" in
        10.14) execute_master_installer ;;
        10.15) execute_master_installer ;;
        11.*)  execute_master_installer ;;
        *)     unsupoorted ;;
    esac
else
    unsupoorted
fi


#### End of [ Main ]
########################################################################################
```
