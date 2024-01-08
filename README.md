# Arch Linux Configuration Guide

Welcome to the Arch Linux Configuration Guide! This comprehensive guide is divided into multiple sections to help you set up and optimize your Arch Linux environment. Feel free to navigate between sections using the provided links.

## Contents

1. [Personal Apps & Bashrc Configuration](#personal-apps--bashrc-configuration)
2. [Fundamentals](#fundamentals)
3. [Multimedia & Utilities](#multimedia--utilities---kde-gnome-cinnamon-terminal-setup-and-more)
4. [Linux Gaming Setup (Wine & Kernel)](#linux-gaming-setup-wine--kernel)
5. [Setup Timeshift and Take a Clean Backup](#setup-timeshift-and-take-a-clean-backup)

## Personal Apps & Bashrc Configuration

#### Install Yay (AUR Helper)

```
mkdir repos
cd repos
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -sic
```

Organize your personal applications and set up a simplified bash environment with these commands:

```bash
# Browser
yay -S --needed firefox chromium google-chrome brave-bin librewolf-bin torbrowser-launcher

# Other Apps
yay -S --needed thunderbird discord telegram-desktop keepassxc mpv qbittorrent bleachbit appimagelauncher onlyoffice-bin visual-studio-code-bin heroic-games-launcher-bin

# Remap Keys on keyboard
yay -S --needed input-remapper-git
sudo systemctl restart input-remapper
sudo systemctl enable input-remapper

# Appimages I use
lmstudio - [lmstudio.ai](https://lmstudio.ai/)
protonup-qt - [ProtonUp-Qt](https://github.com/DavidoTek/ProtonUp-Qt/releases/)

# Config .bashrc
alias ls='eza -al --color=always --group-directories-first'
alias pi='sudo pacman -S'
alias pr='sudo pacman -R'
alias prr='sudo pacman -Rns'
eval "$(starship init bash)"
```

## Fundamentals

If you are utilizing a desktop environment, utilize its built-in Wi-Fi capabilities, or connect via Ethernet. Otherwise, install one of the following applications:

```bash
sudo pacman -S --needed nmcli     # For NetworkManager CLI
or
sudo pacman -S --needed iwctl     # For interacting with iwd (iNet Wireless Daemon)
```

#### Automatic Package Cache Cleanup

Implement a Pacman hook to automatically clear the package cache when necessary.

```
sudo pacman -S --needed pacman-contrib
sudo mkdir /etc/pacman.d/hooks
sudo nano /etc/pacman.d/hooks/clean_package_cache.hook
paste the following inside clean_package_cache.hook
~~~
[Trigger]
Operation = Upgrade
Operation = Install
Operation = Remove
Type = Package
Target = *
[Action]
Description = Cleaning pacman cache...
When = PostTransaction
Exec = /usr/bin/paccache -rk 2
~~~
save & exit
```

#### Optimize Arch Linux Mirrors

Improve the performance of your Arch Linux system by optimizing mirror configurations. Follow these steps to achieve faster downloads:

```
sudo pacman -S --needed reflector
sudo reflector --verbose --country 'India' -l 15 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
```

#### Essential System Packages

Install essential packages for a well-rounded Arch Linux system:

```
# Core System Components
sudo pacman -S --needed linux-headers dkms ufw timeshift

# General Utilities and Tools
sudo pacman -S --needed jshon expac git wget acpid avahi net-tools xdg-user-dirs cronie

# Enable System Services
systemctl enable acpid avahi-daemon systemd-timesyncd
systemctl enable fstrim.timer
systemctl enable cronie.service
```

#### Networking Setup

Configure networking on your Arch Linux system with the following commands:

```
# Install NetworkManager and related tools
sudo pacman -S --needed networkmanager networkmanager-openvpn networkmanager-pptp networkmanager-vpnc

# Enable NetworkManager service
sudo systemctl enable NetworkManager
```

#### AMD Graphics Driver

Install the necessary graphics driver components for AMD GPUs:

```
sudo pacman -S --needed mesa lib32-mesa libva-mesa-driver lib32-libva-mesa-driver mesa-vdpau lib32-mesa-vdpau \
vulkan-icd-loader lib32-vulkan-icd-loader vulkan-radeon lib32-vulkan-radeon libva-vdpau-driver lib32-libva-vdpau-driver
opencl-clover-mesa lib32-opencl-clover-mesa opencl-rusticl-mesa lib32-opencl-rusticl-mesa rocm-opencl-runtime
```

#### Archive and File System Utilities

Install essential archive and file system utilities with the following commands:

```
# Compression Utilities
sudo pacman -S --needed p7zip unrar unarchiver unzip unace xz rsync

# File System Utilities
sudo pacman -S --needed nfs-utils cifs-utils ntfs-3g exfat-utils gvfs udisks2
```

#### Multimedia Codecs

Enhance multimedia codec support by installing the required packages:

```
sudo pacman -S gst-libav gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly gstreamer-vaapi x265 x264 lame --needed
```

#### Sound System Setup

Configure the sound system on your Arch Linux system with the following commands:

```
# Sound System Components
sudo pacman -S --needed alsa-utils pipewire pipewire-pulse pipewire-jack wireplumber

# Sound Control for Qt-based Desktops (e.g., KDE Plasma or LXQt)
sudo pacman -S --needed pavucontrol-qt

# Sound Control for GTK-based Desktops (other desktop environments)
sudo pacman -S --needed pavucontrol
```

#### Manage Audio Output Streams with Helvum (optional)

If you have multiple headphones/speakers and need to manage audio output streams, install Helvum. Helvum is a graphical patchbay for PipeWire, allowing you to create and remove connections between applications and/or devices to reroute audio, video, and MIDI data where needed.

```
sudo pacman -S --needed helvum
```

#### Fonts Installation for Compatibility

Install essential fonts, especially required for Electron apps or Steam Proton gaming, and additional Nerd Fonts for a richer experience:

```
# Common Fonts
sudo pacman -S --needed ttf-dejavu ttf-liberation noto-fonts ttf-caladea ttf-carlito ttf-opensans otf-overpass ttf-roboto tex-gyre-fonts ttf-ubuntu-font-family \
ttf-linux-libertine freetype2 terminus-font ttf-bitstream-vera ttf-droid ttf-fira-mono ttf-fira-sans ttf-freefont ttf-inconsolata ttf-liberation libertinus-font \
adobe-source-sans-fonts adobe-source-serif-fonts adobe-source-sans-pro-fonts cantarell-fonts opendesktop-fonts

# Nerd Fonts for terminal
sudo pacman -S --needed ttf-firacode-nerd ttf-jetbrains-mono-nerd ttf-cascadia-code-nerd ttf-dejavu-nerd ttf-roboto-mono-nerd ttf-sourcecodepro-nerd
```

#### Input Device Drivers

Install the necessary packages to ensure proper functionality of input devices. Even if not immediately required, these packages do not cause harm by being installed.

```
# Common Input Drivers
sudo pacman -S --needed xf86-input-synaptics xf86-input-libinput xf86-input-evdev

# Additional Driver for Virtual Machines
sudo pacman -S --needed xf86-input-vmmouse   # Required when installing inside a virtual machine
```

#### Wi-Fi Connection Setup (Especially for Laptops)

Enable Wi-Fi connectivity, particularly for laptops, by installing the necessary tools:

```pi
sudo pacman -S --needed wireless_tools wpa_supplicant ifplugd dialog
```

#### Bluetooth Support

Adds Bluetooth Support (Especially for Laptops)

```
# Install Bluetooth Packages
sudo pacman -S --needed bluez bluez-utils

# Enable Bluetooth Service
systemctl enable bluetooth
```

#### Granting Read/Write Permissions to Linux Hard Drives

To grant read and write permissions to a Linux hard drive, use the chmod command in the terminal.

```
sudo chown -v your_username:your_username /media/your_external_drive
```

#### Improve Read/Write Speeds on Linux Hard Drives

Boost Linux Hard Drive Speeds: Add "notime" & "discard=async" in fstab
Enhance read/write speeds by editing /etc/fstab:

```
UUID=<your_UUID> /mnt/your_mount btrfs rw,noatime,ssd,discard=async,space_cache=v2 0 0
```

## Multimedia & Utilities - KDE, Gnome, Cinnamon, Terminal Setup, and More

Optimize your multimedia and utility experience by installing various desktop environments, applications, and configuring essential tools:

#### KDE Apps and Themes

```
sudo pacman -S --needed packagekit-qt5 ark okular kate gwenview spectacle kalk kvantum kdeconnect sshfs kcolorchooser filelight plasma-systemmonitor partitionmanager ktorrent
sudo pacman -S --needed materia-kde materia-gtk-theme capitaine-cursors fcitx5-breeze kvantum-theme-materia
```

#### Gnome Apps, Extensions, and Themes

```
sudo pacman -S --needed gnome-text-editor gnome-tweaks gnome-calculator gnome-calendar gnome-photos gnome-sound-recorder gnome-weather gnome-system-monitor gparted gnome-disk-utility
sudo pacman -S --needed gnome-shell-extension-appindicator gnome-shell-extension-arc-menu gnome-shell-extension-caffeine gnome-shell-extension-dash-to-panel  gnome-shell-extensions
sudo pacman -S --needed arc-gtk-theme arc-solid-gtk-theme arc-icon-theme papirus-icon-theme
```

#### Cinnamon Desktop Apps & Themes

```
yay -S --needed pix xviewer webapp-manager xed xreader galculator gnome-screenshot gparted webkit2gtk
yay -S --needed mint-themes mint-y-icons mint-backgrounds
```

#### Setup Terminal, Prompt, Neovim, and LazyVim

```
sudo pacman -S --needed bash-completion alacritty starship eza neovim fd ripgrep xclip python-pynvim nodejs npm
# Make a backup of your current Neovim files:

mv ~/.config/nvim{,.bak}
mv ~/.local/share/nvim{,.bak}
mv ~/.local/state/nvim{,.bak}
mv ~/.cache/nvim{,.bak}
# Clone the starter, remove .git, and run Neovim:

git clone https://github.com/LazyVim/starter ~/.config/nvim
rm -rf ~/.config/nvim/.git
nvim

```

#### Switch to Other Shells

```
# Zsh (Z Shell)
sudo pacman -S --needed zsh zsh-completions
sudo chsh -s /usr/bin/zsh swapnanil

# Fish (Friendly Interactive Shell)
sudo pacman -S --needed fish
sudo chsh -s /usr/bin/fish swapnanil

```

#### Android Debug Bridge (ADB) & Firmware Management

To enable the Android Debug Bridge for your user, use the following commands:

```# Install ADB and Udev rules for Android
sudo pacman -S --needed android-tools android-udev

# Add your user to the adbusers group
sudo usermod -aG adbusers swapnanil
Common ADB commands:
# Check connected devices
adb devices
ac
# Reboot into fastboot mode
adb reboot fastboot

# Reboot into recovery mode
adb reboot recovery

# Sideload a package
adb sideload <filename>

```

If you intend to flash firmware or custom images to your Samsung device, follow these steps:

```
# Install Heimdall for Samsung devices
sudo pacman -S --needed heimdall
Common Heimdall commands:

# Print partition information
heimdall print-pit

# Flash recovery image without rebooting
heimdall flash --RECOVERY recovery.img --no-reboot

```

## Linux Gaming Setup (Wine & Kernel)

#### Install Steam

```
sudo pacman -S --needed steam
```

#### Install Wine & Wine Dependencies

```
sudo pacman -S --needed gamemode lib32-gamemode wine-staging wine-mono wine-gecko winetricks cabextract unzip \
giflib lib32-libnm lib32-giflib libpng lib32-libpng libldap lib32-libldap gnutls lib32-gnutls \
mpg123 lib32-mpg123 openal lib32-openal v4l-utils lib32-v4l-utils libpulse lib32-libpulse libgpg-error \
lib32-libgpg-error alsa-plugins lib32-alsa-plugins alsa-lib lib32-alsa-lib libjpeg-turbo lib32-libjpeg-turbo \
sqlite lib32-sqlite libxcomposite lib32-libxcomposite libxinerama lib32-libgcrypt libgcrypt lib32-libxinerama \
ncurses lib32-ncurses ocl-icd lib32-ocl-icd libxslt lib32-libxslt libva lib32-libva gtk3 \
lib32-gtk3 gst-plugins-base-libs lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader \
cups-filters libexif libmikmod libpaper liburing qpdf sdl12-compat sdl_net sdl_sound cups dosbox samba \
lib32-libxss lib32-nspr lib32-nss lsb-release lsof usbutils xorg-xrandr zenity python-evdev vkd3d lib32-vkd3d faudio lib32-faudio \
lib32-libxxf86vm lib32-libxml2 lib32-openssl nss-mdns lib32-gstreamer gstreamer gsm lib32-fontconfig \
lib32-glu lib32-libsm lib32-libice gst-plugins-good lib32-gst-plugins-base lib32-gst-plugins-good sane
```

#### Install Lutris & Lutris Dependencies

```
sudo pacman -S --needed lutris python-gobject python-requests python-pillow python-yaml python-setproctitle python-distro python-evdev psmisc p7zip curl cabextract soundfont-fluid libgirepository gobject-introspection
```

#### Install FPS Overlay

```
sudo pacman -S --needed mangohud lib32-mangohud goverlay
```

#### Common Game Dependencies (Compilation takes time)

```
yay -S --needed ttf-ms-win11-auto lib32-gsm
```

#### Use Custom Kernel & Wine

"Installing a faster kernel could get you more if not same fps as a debloated windows installation"
[Linux-Tkg](https://github.com/Frogging-Family/linux-tkg) is always recommended
Watch [A1RM4X](https://youtu.be/QIEyv-Pnh0w?si=xBIZg6HLMzp9aP2X) video to install & setup

[wine-tkg-git](https://github.com/Frogging-Family/wine-tkg-git/actions/workflows/wine-arch.yml) is a custom wine-staging build recommended to install if you want absolute wine gaming performance. you must be logged in to github to access prebuilt.

#### Davinci Resolve (AMD) GPU Prerequisites

```
sudo pacman -S --needed libxcrypt-compat rocm-core libarchive xdg-user-dirs patchelf rocm-opencl-runtime
vim /etc/environment
add the line
ROC_ENABLE_PRE_VEGA=1

save and exit
```

## Setup Timeshift and Take a Clean Backup

1. After completing all above steps, Open Timeshift from the application menu.
2. If this is your first time using Timeshift, click "Set up" to start the configuration process.
3. Choose the backup plugin you want to use. You have two options: Rsync or Btrfs. Both are excellent choices, but you might want to choose Rsync if you're backing up an external hard drive.
4. Specify a location for your backups. You can either enter the path manually or click "Browse" to navigate to it using a file explorer.
5. Add any directories that you want Timeshift to exclude from your backup by clicking "Add directory."
6. Click "OK" to save your changes and close the configuration window.
7. Create a new backup by clicking "Create Backup" or exit the app if you're not ready to back up just yet. You can also create a new backup later using the command line interface (CLI).

   - `timeshift --create`: This command creates a new backup of your system.
   - `timeshift --list`: This command lists all available backups.
   - `timeshift-cli backup delete <timestamp>`: This command deletes a specific backup by its timestamp.
   - `timeshift --delete --snapshot '[snapshot]'`: This command restores your system from a specific backup directory.

#### Example

````
timeshift --list
timeshift --list --snapshot-device /dev/sda1
timeshift --create --comments "after update" --tags D
timeshift --restore
timeshift --restore --snapshot '2014-10-12_16-29-08' --target /dev/sda1
timeshift --delete --snapshot '2014-10-12_16-29-08'
timeshift --delete-all```
````
