# Debian Linux (including Kali, Ubuntu, PopOS) installation notes, customisation, hints, guides and troubleshooting information.

## Install Common Utilities and Applications
- Most applications below require a desktop environment installed such as GNOME, LXDE etc.
- Later versions of Debian or Ubuntu (Ubuntu 20.04+) should not require additional apt sources to install the packages listed below.
- Copy and paste the commands below directly into a terminal to batch install the applications if required.

```terminal
# Network and system terminal CLI tools
sudo apt-get -y install net-tools curl wget nmap htop

# Client smb file share access tools
sudo apt-get -y install cifs-utils smbclient

# Tools for mounting different filesystems ExFat NTFS
sudo apt-get -t install exfat-fuse exfat-utils ntfs-3g

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

# Image editor tools
sudo apt-get -y install gimp
sudo apt-get -y install inkscape

# Terminal image viewer
sudo apt-get -y install feh

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
sudo apt-get -y dist-upgrade
sudo apt-get -y autoremove
sudo apt-get -y autoclean
sudo apt-get -y install --fix-broken
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
- Debian 12 Bookworm GNOME desktop loging error "Oops something went wrong..."
- Drop to terminal `CTRL+ALT+F3`
- Reinstall gnome-shell and gnome-session `sudo apt -y install --reinstall gnome-shell gnome-session`
- Return to GNOME login and try again `CTRL+ALT+F1`
- If reinstalling shell and session does not work you could try removing the users .config, .cache and .local folders.
```terminal
rm -rf /home/your_username/.config
rm -rf /home/your_username/.local
rm -rf /home/your_username/.cache
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

## Laptop Specific - Power Management
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

## Debian microcode
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
```bash
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

### Debian 12 Bookworm contrib non-free
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
### Docker Install
```terminal
# Install as per https://docs.docker.com/engine/install/debian/
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Add docker group so docker does not require sudo
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker

# Permission fix
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R

# Test Docker install
docker run hello-world
```

### Install ZSH shell
```terminal
sudo apt-get -y install zsh
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

### Custom prompt for ZSH shell when running the Miniconda
Edit `~/.zshrc` and append to end of file
```terminal
# Prevent conda from changing the prompt
export CONDA_CHANGEPS1=false
CONDA_PATH="/home/yourusername/miniconda3" 

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/mark/miniconda3/bin/conda' 'shell.zsh' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "$CONDA_PATH/etc/profile.d/conda.sh" ]; then
        . "$CONDA_PATH/etc/profile.d/conda.sh"
    else
        export PATH="$CONDA_PATH/bin:$PATH"
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
prompt_symbol='%F{white}'$prompt_symbol'%F{%(#.blue.green)}'

# Modify the PROMPT variable to include conda env
if [[ -n $CONDA_DEFAULT_ENV ]]; then
    PROMPT=$'%F{%(#.blue.green)}┌──${debian_chroot:+($debian_chroot)─}${VIRTUAL_ENV:+($(basename $VIRTUAL_ENV))─}${CONDA_DEFAULT_ENV:+($(basename $CONDA_DEFAULT_ENV))─}(%B%F{%(#.red.blue)}%n'$prompt_symbol$'%m%b%F{%(#.blue.green)})-[%B%F{reset}%(6~.%-1~/…/%4~.%5~)%b%F{%(#.blue.green)}]\n└─%B%(#.%F{red}#.%F{blue}$)%b%F{reset} '
else
    PROMPT=$'%F{%(#.blue.green)}┌──${debian_chroot:+($debian_chroot)─}${VIRTUAL_ENV:+($(basename $VIRTUAL_ENV))─}(%B%F{%(#.red.blue)}%n'$prompt_symbol$'%m%b%F{%(#.blue.green)})-[%B%F{reset}%(6~.%-1~/…/%4~.%5~)%b%F{%(#.blue.green)}]\n└─%B%(#.%F{red}#.%F{blue}$)%b%F{reset} '
fi

```
Prompt Result (base) is current active Conda environment
```terminal
┌──(base)─(user@machinehost)-[~]
└─$ 
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

## Linux commands and key file locations

```bash
# Show user accounts and groups
cat /etc/passwd
```

```bash
# Show user shadow password file and hashes
sudo cat /etc/shadow
```

```bash
# Show user sudoers privileges
sudo cat /etc/sudoers

# List users in sudo group
cat /etc/group | grep 'sudo'

# Edit sudo (must be root to run visudo) su to root
visudo
```

```bash
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

```bash
# Show open network ports related programs/services
sudo netstat -tulpan

# Continuously watch for `ESTABLISHED` connections (update every 3 seconds) 
sudo watch -n 3 "netstat -tulpan | grep ESTABLISHED"
```

```bash
# curl ignore SSL certificate errors and follow redirects including 301
curl -kL https://address
```

```bash
# List cron jobs
crontab -l

# List root user cron jobs
crontab -u root -l

# List timers that a loaded to run
systemctl list-timers
```

```bash
# Show offered fileshare mounts
showmount -e 192.168.0.10
# result return ie. /srv/thisdir
# To mount make local directory to mount fileshare
mkdir /mnt/thisdir
# Mount filesystem
mount -t nfs 192.168.0.10:/srv/nfs /mnt/thisdir
```

```bash
# Monitor log events -f --follow show only the most recent
sudo journalctl -f

# View all logs for sudo
sudo journalctl -e /usr/bin/sudo
```
