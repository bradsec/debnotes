# Debian Linux (including Kali, Ubuntu, PopOS) installation notes, customisation, hints, guides and troubleshooting information.

- For a quick way to install other Debian linux applications refer to [bradsec/debapps](https://github.com/bradsec/debapps)  
- For a quick way to customise GNOME desktop refer to [bradsec/debgnome](https://github.com/bradsec/debgnome)

## Install Common Utilities and Applications
- Most applications below require a desktop environment installed such as GNOME, LXDE etc.
- Later versions of Debian or Ubuntu (Ubuntu 20.04+) should not require additional apt sources to install the packages listed below.
- Copy and paste the commands below directly into a terminal to batch install the applications if required.

```terminal
# Network and system terminal CLI tools
sudo apt-get -y install net-tools nfs-common curl wget nmap tmux htop nvtop

# Client smb file share access tools
sudo apt-get -y install cifs-utils smbclient

# Tools for mounting different filesystems ExFat NTFS
sudo apt-get -y install exfat-fuse ntfs-3g

# Development and version control tools
sudo apt-get -y install git

# Add kernel and module build tools
sudo apt-get -y install build-essential dkms linux-headers-$(uname -r)

# Add additional developer make tools
sudo apt-get -y install make sassc gettext

# Python3 and pip3
sudo apt-get -y install python3 python3-pip python3-gpg 
pip3 install --upgrade pip
pip3 install setuptools

## Other dependencies for Stable Diffusion Automatic1111 webui
sudo apt-get -y install glibc-source libgoogle-perftools4 libtcmalloc-minimal4

# Text editor tools
sudo apt-get -y install vim

# Disk partition tools
sudo apt-get -y install gparted 

# Video player, tools and codecs
sudo apt-get -y install vlc
sudo apt-get -y install handbrake
sudo apt-get -y install ffmpeg
sudo apt-get -y install libavcodec-extra gstreamer1.0-libav gstreamer1.0-plugins-ugly gstreamer1.0-vaapi

# Audio file tools
sudo apt-get -y install audacity

# Backup tools
sudo apt-get -y install timeshift

# Privacy and system management tools
sudo apt-get -y install bleachbit
sudo apt-get -y install stacer

# File archive compression tools
sudo apt-get -y install rar unrar

# Remote desktop access client
sudo apt-get -y install remmina

# Image editor tools
sudo apt-get -y install gimp
sudo apt-get -y install inkscape

# Terminal image viewer
sudo apt-get -y install feh

# Terminal copy and paste tool
sudo apt-get -y install xclip 

# Screenshot Screen Capture and Recording tools
sudo apt-get -y install flameshot
sudo apt-get -y install kazam

# Add additional fonts
sudo apt-get -y install ttf-mscorefonts-installer
sudo apt-get -y install fonts-crosextra-carlito fonts-crosextra-caladea
sudo apt-get -y install ttf-bitstream-vera
sudo apt-get -y install fonts-firacode -y

# Update, Upgrade, and Cleanup and Fix Broken Installs
sudo apt-get update
sudo apt-get -y upgrade
sudo apt-get -y autoremove
sudo apt-get -y autoclean
sudo apt-get -y install --fix-broken
```
## Anti-Virus ClamAV
```terminal
# Install requirements
sudo apt-get -y install clamav
sudo apt-get -y install clamav-daemon

# Update signatures
sudo systemctl stop clamav-freshclam
sudo -u clamav freshclam
sudo systemctl start clamav-freshclam

# Restart Daemon
sudo systemctl restart clamav-daemon
```

## Printer support
```terminal
# Ref: https://wiki.debian.org/SystemPrinting
sudo apt-get -y install printer-driver-all

# CUPS is commonly installed with the default install packages Debian 11 and 12
sudo apt-get -y cups
```


## Samba File Sharing (example sharing your home directory with write permissions)
```
# Install Samba server package
sudo apt-get -y install samba

# Backup original Samba config file
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak

# Edit /etc/samba/smb.conf with preferred editor nano, vim etc.
# Append share details to bottom of smb.conf file.

[shared-folder] # change to preferred reference name this is referenced when connecting ie. smb://server-ip/shared-folder
   path = /home/your_username
   available = yes
   valid users = your_username
   read only = no
   browsable = yes
   public = yes
   writable = yes
   create mask = 0644
   directory mask = 0755

# Save file and exit
# Restart Samba server
sudo systemctl restart smbd

# Create a Samba access password for the user specified in smb.conf valid users section this does not affect your normal system access password.
sudo smbpasswd -a your_username

# Notes - If running a firewall you may need to allow Samba
sudo ufw allow Samba

# Connect to share from a client machine
# Windows: \\debian_hostname_or_ip\shared-folder
# macOS and Linux: smb://debian_hostname_or_ip/shared-folder
```

## GNOME Specific Improve Desktop Appearance
```terminal
# Install GNOME tweaks
sudo apt-get -y install gnome-tweaks
```
### Quick pimp GNOME default Terminal color scheme

```Terminal
#!/bin/bash

# Get the list of profiles
PROFILES=$(dconf list /org/gnome/terminal/legacy/profiles:/)

# Optional - Set DEFAULT_PROFILE manually by getting profile ID using `dconf list /org/gnome/terminal/legacy/profiles:/`
# Method below will attempt to extract the default profile, assuming default is first

DEFAULT_PROFILE=$(gsettings get org.gnome.Terminal.ProfilesList default | tr -d \')

PROFILE="/org/gnome/terminal/legacy/profiles:/:$DEFAULT_PROFILE"

# Scheme similar to Kali dark color
PALETTE="['#101010', '#ff6685', '#aaffaa', '#ffe156', '#00a2ff', '#c594c5', '#00ffff', '#cccccc', '#666666', '#ff669d', '#aaffaa', '#ffe156', '#00a2ff', '#c594c5', '#00ffff', '#ffffff']"
FOREGROUND_COLOR="'#eeeeec'"
BACKGROUND_COLOR="'#101010'"
BOLD_COLOR="'#babdb6'"

# Set the color scheme
dconf write $PROFILE/palette "$PALETTE"
dconf write $PROFILE/foreground-color "$FOREGROUND_COLOR"
dconf write $PROFILE/background-color "$BACKGROUND_COLOR"
dconf write $PROFILE/bold-color "$BOLD_COLOR"
dconf write $PROFILE/bold-color-same-as-fg "false"
dconf write $PROFILE/use-theme-colors "false"
```

### GNOME Extensions
- https://extensions.gnome.org
- Add browser extension (works with Firefox or Chrome)
  - Activate - User Themes
  - Activate - Dash-to-Dock
- Additional settings under Tweaks applcation
  - Appearance and Extensions tabs

### GNOME Troubleshooting
- Debian 12 Bookworm GNOME desktop logging in error "Oops something went wrong..."
- Drop to terminal `CTRL+ALT+F3`
- Reinstall gnome-shell and gnome-session
```terminal
sudo apt -y install --reinstall gnome-shell gnome-session`
```
- Return to GNOME login and try again `CTRL+ALT+F1`
- If reinstalling shell and session does not work you could try removing the users .config, .cache and .local folders.
```terminal
rm -rf /home/$USER/.config
rm -rf /home/$USER/.local
rm -rf /home/$USER/.cache
```
  
### Improve desktop appearance with improved themes/icons

```terminal
# Install Orchis-theme and papirus-icon-theme
sudo apt-get -y install papirus-icon-theme
git clone https://github.com/vinceliuice/Orchis-theme.git
./Orchis-theme/install.sh 

# Apply the themes
gsettings set org.gnome.desktop.interface gtk-theme Orchis-Dark
gsettings set org.gnome.desktop.wm.preferences theme Orchis-Dark
gsettings set org.gnome.shell.extensions.user-theme name Orchis-Dark
gsettings set org.gnome.desktop.interface icon-theme Papirus

# Set background to solid RGB color
gsettings set org.gnome.desktop.background picture-uri ''
gsettings set org.gnome.desktop.background primary-color 'rgb(18, 108, 210)'

# Set font and window scaling 2x
gsettings set org.gnome.settings-daemon.plugins.xsettings overrides "[{'Gdk/WindowScalingFactor', <2>}]"

```

### Terminal appearance (hide menu bar and title header bar)

```terminal
# Terminal appearance improvements
gsettings set org.gnome.Terminal.Legacy.Settings default-show-menubar false
gsettings set org.gnome.Terminal.Legacy.Settings headerbar false

# Above headerbar false not working (workaround)
# Note: This does prevent drag resizing of the terminal window. 
# The window size has been set manually in preferences

# Install xdotool https://packages.debian.org/buster/x11/xdotool
sudo apt install xdotool

# Add the following to .zshrc or bash profile
if [ "$TERM" = "xterm-256color" ]; then
  xprop \
    -id $(xdotool getactivewindow) \
    -f _MOTIF_WM_HINTS 32c \
    -set _MOTIF_WM_HINTS "0x2, 0x0, 0x0, 0x0, 0x0"
fi

# Restart terminal and headerbar should be gone. To move terminal around screen right click and show menu.
```

## Simple UFW firewall with GUI management
```terminal
sudo apt-get -y install ufw gufw
sudo ufw enable
```

## Laptop Specific - Power Management (versions < Debian 12 Bookworm)
```terminal
sudo apt-get -y install tlp-get -y
```

## Ubuntu Specific
```terminal
sudo apt-get -y install ubuntu-restricted-extras
```

## Nvidia Graphics Specific
```terminal
# Notes:
# - Debian 12 Bookworm enable or append 'contrib non-free' to etc source in `/etc/apt/sources.list`
# - Then run `sudo apt update`  
# - Also if BIOS Secure boot is enable you may have some issues with the system failing to boot
# unless you correctly sign the modules as suggested: https://wiki.debian.org/NvidiaGraphicsDrivers

sudo apt-get -y install nvidia-detect
sudo nvidia-detect
sudo apt-get -y install nvidia-driver
sudo apt-get -y install nvidia-cuda-toolkit nvidia-cuda-dev
```

## File manager show dotfiles or hidden files
Shortcut `ctrl-h`

## Debian 11 microcode
- Open Synaptic Package Manager
- Search "microcode"
- Install appropriate microsoft AMD or Intel

## Debian enable snap and flatpak support
- Open "Software" application
- Search in Software application for "software"
- Click "Software"
- Scroll down to Add-ons and select Flatpak and Snap Support

## Debian Swappiness
- If system has increased memory number can be reduced and may improve performance.
- Check current (default 60)  
`cat /proc/sys/vm/swappiness`
- Modify by editting the /etc/sysctl.conf and add line below and reboot.  
`vm.swappiness=10`

```terminal
# Copy paste update swappiness
# Set new value
NEW_VAL=10
# Adds value if it doesn't exist
sudo grep -q 'vm.swappiness=' /etc/sysctl.conf || echo "vm.swappiness=${NEW_VAL}" | sudo tee -a /etc/sysctl.conf
# Updates value if it already exists
sudo sed -i "s/^vm.swappiness=.*/vm.swappiness=${NEW_VAL}/" /etc/sysctl.conf
# Reload configuration
sudo sysctl --system
```

## Debian GRUB increase Boot Speed and add splash screen if GNOME installed.

```terminal
sudo sed -i 's/^GRUB_TIMEOUT=.*/GRUB_TIMEOUT=2/' /etc/default/grub
sudo sed -i 's/^GRUB_CMDLINE_LINUX_DEFAULT=.*/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"/' /etc/default/grub
sudo update-grub
```

## Create home directories
```terminal
mkdir ~/.themes
mkdir ~/.icons
```

# Debian Distro Minimal Install Notes

When prompted unselect all desktop and package options *(use mouse with graphical install or spacebar to unselect normal install)*. The base install should boot to a terminal as no X Window desktop environment should have been selected.

### `sudo` command not found and/or default user not in the sudo group

***When installing is you leave root password blank this should disable the root account and install sudo for the user you create. Otherwise follow the steps below to enable sudo on a selected user account.***

The mimimal install may mean the `sudo` command may not be installed.
The following commands install the sudo package and adds the default (logged in user) to the sudo group.

```terminal
su -c 'apt-get update && apt-get -y install sudo && \
/sbin/adduser $USER sudo && \
exec su -l $USER'
```

To undo the above (remove user from group sudo and remove package sudo) run the following:  
```terminal
su -c 'gpasswd -d $USER sudo && \
apt-get -y remove sudo && exec su -l $USER'
```

### Debian 12 Bookworm (quick method) add contrib non-free
```terminal
# Append contrib and non-free to /etc/apt/sources.list
sudo sed -i '/^deb .* non-free-firmware$/ s/$/ contrib non-free/; /^deb-src .* non-free-firmware$/ s/$/ contrib non-free/' /etc/apt/sources.list
sudo apt update
```

### Updating Debian
```terminal
sudo apt-get update && sudo apt-get -y dist-upgrade
```

### Debian base install - ifconfig, shutdown, reboot, halt and other commands not working

It may be that `/sbin` is missing from the user $PATH variable  
To check current path variables run:
- `echo $PATH` or `env | grep PATH`

Simply edit the `/etc/profile` file and append `/sbin` to the second PATH variable.
- Using nano `sudo nano /etc/profile`
- Using vi `sudo vi /etc/profile`  

*You may need to logout and back in to initialize the new PATH variables.*

Alternatively use the direct path

```terminal
sudo /sbin/reboot
sudo /sbin/halt
sudo /sbin/shutdown
```

Alternatively use the systemctl commands as follows:

```terminal
sudo systemctl reboot
sudo systemctl halt
sudo systemctl poweroff
```

### Install GNOME Desktop Environment (core only, no games, office apps etc.)

```terminal
sudo apt-get -y install gnome-core
```

### Install XFCE Desktop Environment
```terminal
sudo apt-get -y install xserver-xorg xfce4 xfce4-goodies
```

### Check boot environment graphical or console/terminal
```terminal
sudo systemctl get-default
```

### Start in graphical environment

```terminal
sudo systemctl set-default graphical.target
```

### Start in terminal/console environment

```terminal
sudo systemctl set-default multi-user.target
```

# SSH Notes

### Install SSH server (Debian base install)

```terminal
sudo apt-get -y install openssh-server
```

### Backup and Regenerate SSH Server Keys (Debian/Kali)

```terminal
sudo mkdir /etc/ssh/backup_keys && \
sudo mv /etc/ssh/ssh_host_* /etc/ssh/backup_keys && \
sudo dpkg-reconfigure openssh-server
```  

# Miscellaneous Notes

### Networking - Show active ports and processes

```terminal
# Command switches [a] all, [t] TCP, [u] UDP, [l] listening, [p] process, [n] numeric 
# Using netstat command
netstat -laptun

# Using ss command
ss -laptun

# Resolve IP addresses
ss -laptur
```

### Disable IPV6 on all interfaces
```terminal
sudo nano /etc/sysctl.conf
```
Add the following line to ```sysctl.conf```:  
`net.ipv6.conf.all.disable_ipv6 = 1`  
*Change all to interface adapter name for individual interfaces*  
Run ```sudo sysctl -p``` to execute changes

### Missing firmware (Debian 11 Bullseye base install)
Check ```/etc/apt/sources.list``` contains ```contrib non-free``` otherwise edit the file append to end of each deb source line and run the following commands.

If `sources.list` is missing contrib non-free:  
```terminal
sudo sed -r -i 's/^deb(.*)$/deb\1 contrib non-free/g' /etc/apt/sources.list
```  

```terminal
sudo apt-get update && sudo apt-get -y install firmware-misc-nonfree
```

If wifi drivers such those in some Intel NUCs are missing try...

```terminal
sudo apt-get -y install firmware-iwlwifi
```

### Remove GRUB timeout, enable splash and disable USB auto suspend power management

```terminal
# Edit /etc/default/grub and ensure the the following lines exist

GRUB_DEFAULT=0
GRUB_TIMEOUT=0
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.autosuspend=-1"

# Run GRUB update
sudo update-grub

# Reboot
sudo reboot

# Check USB power management has been changed to -1
cat /sys/module/usbcore/parameters/autosuspend
```

### Script to install latest linux Go version
```terminal
#!/bin/bash

# Function to check if the script is running as root
check_root() {
    if [[ $EUID -ne 0 ]]; then
        echo "This script must be run as root." >&2
        exit 1
    fi
}

# Function to download a file
function download_file() {
    local dst_file=${1}
    local src_url=${2}

    # Check if the source URL is blank
    if [[ -z ${src_url} ]]; then
        echo "Unable to get the latest application source URL. The application source URL may have changed or moved."
        exit 1
    fi

    echo "Downloading file..."
    echo "SRC: ${src_url}"
    echo "DEST: ${dst_file}"

    # Check if the source URL is valid
    if [[ ! ${src_url} =~ ^(http|https|ftp):// ]]; then
        echo "Invalid source URL. Only URLs starting with 'http://', 'https://', or 'ftp://' are supported."
        exit 1
    fi

    if wget --user-agent=Mozilla --content-disposition -c -E -O \
    "${dst_file}" "${src_url}" -q --show-progress --progress=bar:force 2>&1; then
        echo
        echo "File successfully downloaded."
    else
        echo "There was a problem downloading the file. Trying another method..."
        echo "Trying wget without resume option..."
        if wget --user-agent=Mozilla --content-disposition -E -O \
            "${dst_file}" "${src_url}" -q --show-progress --progress=bar:force 2>&1; then
            echo
            echo "File successfully downloaded."
        elif curl -L -J "${src_url}" -o "${dst_file}" --progress-bar; then
            echo
            echo "File successfully downloaded."
        else
            echo "There was a problem downloading the file. Check URL and source file."
            exit 1
        fi
    fi
}

# Check if the script is run as root
check_root

# Variables for download
from_url="https://go.dev$(curl -s https://go.dev/dl/ | \
        grep linux | grep $(dpkg --print-architecture) -A 0 | sed -r 's/.*href="([^"]+).*/\1/g' | awk 'NR==1')"
save_file="/tmp/golang.tar.gz"

# Download the file
download_file ${save_file} ${from_url}

# Extract the downloaded file
rm -rf /usr/local/go && tar -C /usr/local -xzf ${save_file}

# Remove temp save file
rm ${save_file}

# Run go version and display the output
/usr/local/go/bin/go version
```

### Docker Install
```terminal
# Remove any older versions
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Setup repo and add official Docker GPG key
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# Install latest version
sudo apt-get install docker-ce docker-compose docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# If required add docker group and current user to docker group
if ! getent group docker > /dev/null; then sudo groupadd docker; fi
sudo usermod -aG docker $USER
newgrp docker

# Verify installation and that the user has permissions to run docker by running hello-world image
docker run hello-world
```

### Install ZSH shell
```terminal
sudo apt-get -y install zsh
```

### Install OhMyZsh and helpful plugins
```terminal
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
#### Edit `~/.zshrc`
Update plugin section - 
```terminal
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)
```

### Example of `.zshrc` with custom prompt
Prompt included in .zshrc below supports Python virtual environments (venv and conda).
  
Example prompt basic (prompt is colored):
```terminal
┌──(conda_base)─(user@hostname)-[/dirpath]
└─$ 
```
#### .zshrc contents
```terminal
export PATH=$HOME/bin:/usr/local/bin:$PATH
export ZSH="$HOME/.oh-my-zsh"
export CONDA_PATH="$HOME/miniconda3"
ZSH_THEME="robbyrussell"

plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)

source $ZSH/oh-my-zsh.sh
export CONDA_CHANGEPS1=false

# >>> conda initialize >>>
__conda_setup="$('$CONDA_PATH/bin/conda' 'shell.zsh' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "$CONDA_PATH/etc/profile.d/conda.sh" ]; then
        . "$CONDA_PATH/etc/profile.d/conda.sh"
    else
        export PATH="$CONDA_PATH/miniconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<

NEWLINE_BEFORE_PROMPT=yes

precmd() {
    # Print a new line before the prompt, but only if it is not the first line
    if [ "$NEWLINE_BEFORE_PROMPT" = yes ]; then
        if [ -z "$_NEW_LINE_BEFORE_PROMPT" ]; then
            _NEW_LINE_BEFORE_PROMPT=1
        else
            print ""
        fi
    fi
}

prompt_symbol=@

# Modify the PROMPT variable to include conda env
prompt_symbol='%F{white}'$prompt_symbol'%F{blue}'

# Function to update the prompt
update_prompt() {
    local prompt_symbol='%F{white}'$prompt_symbol'%F{blue}'
    local conda_env_name=''
    local env_name=''

    if [[ -n $VIRTUAL_ENV ]]; then
        env_name=$(basename "$VIRTUAL_ENV")
    fi

    if [[ -n $CONDA_DEFAULT_ENV ]]; then
        conda_env_name=$(basename "$CONDA_DEFAULT_ENV")
    fi

    # Add conditional for different shells
    if [[ $SHELL == *"zsh"* ]]; then
        PROMPT="%F{blue}┌──${debian_chroot:+($debian_chroot)─}${env_name:+(venv_$env_name)─}${conda_env_name:+(conda_$conda_env_name)─}(%B%F{%(#.red.green)}%n$prompt_symbol%m%b%F{blue})-[%B%F{reset}%~%b%F{blue}]
%F{blue}└─%B%(#.%F{red}#.%F{cyan}$)%b%F{reset} "
    elif [[ $SHELL == *"bash"* ]]; then
        PS1="\[\e[34m\]┌──${debian_chroot:+($debian_chroot)─}${env_name:+(venv_$env_name)─}${conda_env_name:+(conda_$conda_env_name)─}\[\e[m\]\[\e[1m\]\[\e[38;5;$(( $(id -u) == 0 ? 1 : 2 ))m\]\u$prompt_symbol\h\[\e[m\]\[\e[34m\]\]-[\[\e[m\]\[\e[1m\]\[\e[0m\]\w\[\e[m\]\[\e[34m\]\]
\[\e[34m\]└─\[\e[m\]\[\e[1m\]\[\e[38;5;$(( $(id -u) == 0 ? 1 : 6 ))m\]#\[\e[m\]\[\e[1m\]\[\e[0m\] "
    fi
}

# Call the update_prompt function initially
update_prompt

# Function to update the prompt when the command is executed
if [[ $SHELL == *"zsh"* ]]; then
    precmd_functions+=(update_prompt)
elif [[ $SHELL == *"bash"* ]]; then
    PROMPT_COMMAND=update_prompt
fi

# Adjust path if required. Example below is for when Go was installed.
export PATH=/usr/local/go/bin:$PATH
```


### Change default user shell to zsh (don't use sudo)  
```terminal
chsh -s $(which zsh)
```

### Load existing `.profile` PATHS etc. while using ZSH
Edit `~/.zshrc` and append the following to end of file
```terminal
[[ -e ~/.profile ]] && emulate sh -c 'source ~/.profile'
```

### Check current user shell
```terminal
ps -p $$
```

### Fix slow lagging VNC and VNC resolution issues on headless Raspberry Pi 4
Edit `/boot/config.txt`
```terminal
#### Comment out the following lines
# Enable DRM VC4 V3D driver
#dtoverlay=vc4-kms-v3d
#max_framebuffers=2

#### Uncomment this line
hdmi_force_hotplug=1

#### Uncomment and modify the following two lines to force higher resolution
# uncomment to force a specific HDMI mode (this will force VGA)
hdmi_group=2
hdmi_mode=82
```

### GNU Privacy Guard (GPG)

- Generate new key
```terminal
gpg --full-generate-key

# Select default key RSA and RSA
# Increase key security/length to 4096
# Set key expiry 0 = Does not expire
# Enter user details
# Enter complex pass phrase
```

- List public keys  
`gpg --list-public-keys`

- List private keys  
`gpg --list-secret-keys`

- Encrypt a file  
```terminal
gpg --encrypt --output encryptedfilename.gpg --recipient someemail@email.net filenametoencrypt
```

- Decrypt a file  
```terminal
gpg --decrypt --output decryptedfilename.txt encryptedfilename.gpg

# Enter private key pass phrase
```

## debian linux helpful commands

```terminal
# Show user accounts and groups
cat /etc/passwd
```

```terminal
# Show user shadow password file and hashes
sudo cat /etc/shadow
```

```terminal
# Show user sudoers privileges
sudo cat /etc/sudoers

# List users in sudo group
cat /etc/group | grep 'sudo'

# Edit sudo (must be root to run visudo) su to root
visudo
```

```terminal
# List network interface details
ip a
ifconfig

# List network address including MAC
ip n
arp -a

# List wireless details
iwconfig

# Show route (routing table)
ip r
route
```

```terminal
# Show open network ports related programs/services
sudo netstat -tulpan

# Continuously watch for `ESTABLISHED` connections (update every 3 seconds) 
sudo watch -n 3 "netstat -tulpan | grep ESTABLISHED"
```

```terminal
# curl ignore SSL certificate errors and follow redirects including 301
curl -kL https://address
```

```terminal
# List cron jobs
crontab -l

# List root user cron jobs
crontab -u root -l

# List timers that a loaded to run
systemctl list-timers
```

```terminal
# Show offered fileshare mounts
showmount -e 192.168.0.10
# result return ie. /srv/thisdir
# To mount make local directory to mount fileshare
mkdir /mnt/thisdir
# Mount filesystem
mount -t nfs 192.168.0.10:/srv/nfs /mnt/thisdir
```

```terminal
# Monitor log events -f --follow show only the most recent
sudo journalctl -f

# View all logs for sudo
sudo journalctl -e /usr/bin/sudo
```

### Disable Enable Suspend Hibernete

```terminal
# Disable sleep suspect on laptop lid close, will take affect upon next reboot.
sudo sed -i '/\[Login\]/a HandleLidSwitch=ignore\nHandleLidSwitchDocked=ignore' /etc/systemd/logind.conf
```

```terminal
# Disable sleep, suspect, hibernate
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

```terminal
# Check status sleep, suspect, hibernate
sudo systemctl status sleep.target suspend.target hibernate.target hybrid-sleep.target
```

```terminal
# Enable sleep, suspect, hibernate
sudo systemctl unmask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

### Networking
Show network interface and address details

```terminal
# Show full network interface details
ip address show
```

```terminal
# Show network interface names only
ip address show | awk '/^[0-9]+:/ {sub(/:$/, "", $2); print $2}'
```

```terminal
# Show interface names with MAC addresses only
ip address show | awk '/^[0-9]+:/ {interface=$2} /link\/ether/ {print interface, $2}'
```

```terminal
# Show only network interfaces with assigned IP addresses
ip address show | awk '/^[0-9]+:/ {interface=$2} /inet / {print interface, $2}'
```

Adding DHCP to a ethernet interface  
- Change `enp0s31f6` to the interface name to configure with DHCP  
Example config `/etc/network/interfaces`  

```terminal
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The ethernet interface to be configured with DHCP
allow-hotplug enp0s31f6
iface enp0s31f6 inet dhcp
```
  
To bring network inferfaces down and up use `ifdown` and `ifup`
```terminal
# Set to down
sudo ifdown enp0s31f6

# Set to up
sudo ifup enp0s31f6
```

## Bad kernal update 16 Feb 2024, 6.1.0-18 causing issues updating or upgrading packages caused by issues in nVidia driver files with 6.1.0.18 kernel

```terminal
# Terminal error after attempting to apt update and upgrade
Building module:
Cleaning build area...
unset ARCH; env NV_VERBOSE=1 make -j12 modules KERNEL_UNAME=6.1.0-18-amd64..............(bad exit status: 2)
Error! Bad return status for module build on kernel: 6.1.0-18-amd64 (x86_64)
Consult /var/lib/dkms/nvidia-current/545.23.08/build/make.log for more information.
Error! One or more modules failed to install during autoinstall.
Refer to previous errors for more information.
dkms: autoinstall for kernel: 6.1.0-18-amd64 failed!
run-parts: /etc/kernel/postinst.d/dkms exited with return code 11
dpkg: error processing package linux-image-6.1.0-18-amd64 (--configure):
 installed linux-image-6.1.0-18-amd64 package post-installation script subprocess returned error exit status 1
Setting up linux-headers-6.1.0-18-amd64 (6.1.76-1) ...
/etc/kernel/header_postinst.d/dkms:
dkms: running auto installation service for kernel 6.1.0-18-amd64.
Sign command: /usr/lib/linux-kbuild-6.1/scripts/sign-file
Signing key: /var/lib/dkms/mok.key
Public certificate (MOK): /var/lib/dkms/mok.pub

Building module:
Cleaning build area...
unset ARCH; env NV_VERBOSE=1 make -j12 modules KERNEL_UNAME=6.1.0-18-amd64..............(bad exit status: 2)
Error! Bad return status for module build on kernel: 6.1.0-18-amd64 (x86_64)
Consult /var/lib/dkms/nvidia-current/545.23.08/build/make.log for more information.
Error! One or more modules failed to install during autoinstall.
Refer to previous errors for more information.
dkms: autoinstall for kernel: 6.1.0-18-amd64 failed!
run-parts: /etc/kernel/header_postinst.d/dkms exited with return code 11
Failed to process /etc/kernel/header_postinst.d at /var/lib/dpkg/info/linux-headers-6.1.0-18-amd64.postinst line 11.
dpkg: error processing package linux-headers-6.1.0-18-amd64 (--configure):
 installed linux-headers-6.1.0-18-amd64 package post-installation script subprocess returned error exit status 1
dpkg: dependency problems prevent configuration of linux-image-amd64:
 linux-image-amd64 depends on linux-image-6.1.0-18-amd64 (= 6.1.76-1); however:
  Package linux-image-6.1.0-18-amd64 is not configured yet.

dpkg: error processing package linux-image-amd64 (--configure):
 dependency problems - leaving unconfigured
dpkg: dependency problems prevent configuration of linux-headers-amd64:
 linux-headers-amd64 depends on linux-headers-6.1.0-18-amd64 (= 6.1.76-1); however:
  Package linux-headers-6.1.0-18-amd64 is not configured yet.

dpkg: error processing package linux-headers-amd64 (--configure):
 dependency problems - leaving unconfigured
Errors were encountered while processing:
 linux-image-6.1.0-18-amd64
 linux-headers-6.1.0-18-amd64
 linux-image-amd64
 linux-headers-amd64
E: Sub-process /usr/bin/dpkg returned an error code (1)
```

## Problem
The following commands fail `sudo apt install --fix-broken` and `sudo dpkg --configure -a` 
```terminal
Hanging on `unset ARCH; env NV_VERBOSE=1 make -j12 modules KERNEL_UNAME=6.1.0-18-amd64`
dpkg: error processing package linux-headers-6.1.0-18-amd64 (--configure):
 installed linux-headers-6.1.0-18-amd64 package post-installation script subprocess returned error exit status 1
dpkg: dependency problems prevent configuration of linux-headers-amd64:
 linux-headers-amd64 depends on linux-headers-6.1.0-18-amd64 (= 6.1.76-1); however:
  Package linux-headers-6.1.0-18-amd64 is not configured yet.

dpkg: error processing package linux-headers-amd64 (--configure):
```

## Solution
Remove bad update package files
```terminal
sudo apt -y purge linux-image-amd64 linux-headers-amd64 linux-image-6.1.0-18-amd64 linux-headers-6.1.0-18-amd64
sudo apt -y autoclean
sudo apt -y autoremove
sudo rm /var/lib/dpkg/info/linux-image-6.1.0-18*
sudo rm /var/lib/dpkg/info/linux-headers-6.1.0-18*
sudo apt -y install --fix-broken
```





