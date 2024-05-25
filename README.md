## Fedora Upgrade and Enhancement Guide
* This list was written for Fedora Workststion and works best with new installations. Ive made sure to include as much as possible for everyone, be it Hardware or what have you.
* If you are a new user then, Welcome! Today is Easter! A Painted Egg lays before you.

### Context: 
* Repositories for RPM Fuusion - Free, Non-Free, NVIDIA, Steam, Tainted 
* Upgrades - Accelerated Hardware, Media Codec, OpenH624, configurations
* Enhancements - Drivers for Intel and AMD, ffmpeg, servers
* Tools and Applications - Gparted, Gnome-Tweaks, Nemo, 7zip, Extension Manager
* Extras and more - Fonts, Icons, and Themes, Groups

* This list holds a small library of Advancements that are up to, relevant and compatible with the Latest Release (Fedora 40)
* I put this list together for myself and others for a quick and easy way to upgrade any system. You can take some of these and interchange with any Distro. ive done so with multiple Debian OSs. (i.e. Nemo FileMan, the Flatpak repo, and Battery Life. ..)

## Set your hostname CLi
* `hostnamectl set-hostname YOUR.HOSTNAME.com`

## Faster DNF
* `sudo nano /etc/dnf/dnf.conf`
* Copy and add the following to your DNF configuration file
```
[main] 
gpgcheck=1 
installonly_limit=3 
clean_requirements_on_remove=true
best__available=true
skip_if_unavailable=true
network_connection=Good
fastestmirror=true
max_parallel_downloads=10 
deltarpm=true
defaultyes=true
keepcache=yes
```
* Note: Fastest Mirror, Network Connection, and Delta RPM can in some cases fail to restore a connection. I find it best to disable for the moment and continue with previous task.
* Usually after a system restart they fire right back up after enabled again. If a problem presents itself the best option would be to either commit temporarily or change =
* It may only be a network disruption. Use at your own discretion.
* 1/0, yes/no, true/false = same input.

## RPM Fusion
* First enable access to the free and non-free repositories. If installing both the Mirror will be from the rpmfusion webpage.
* free
* `https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-40.noarch.rpm`
* non-free
* `https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-40.noarch.rpm`

* Mirror
* `sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm`

## RPM Firmware
* If you would like the Tainted repositories add the following
* `sudo dnf install rpmfusion-nonfree-release-tainted`
* `sudo dnf --repo=rpmfusion-nonfree-tainted install "*-firmware"`

## Update and Restart Now
* DNF update is similar to Debian `sudo apt-get update && sudo apt-get upgrade`
* `sudo dnf update -y`
* `sudo dnf -y upgrade --refresh`

## Media Codec
* It is Not Advised to cut or edit the following commands due to the possible resulting broken packages and --fix-broken ,, --allowerasing W: error flags. Please use the Full Syntax
* `sudo dnf groupupdate 'core' 'multimedia' 'sound-and-video' --setopt='install_weak_deps=False' --exclude='PackageKit-gstreamer-plugin' --allowerasing && sync`
* `sudo dnf install gstreamer1-plugins-{bad-\*,good-\*,base} gstreamer1-plugin-openh264 gstreamer1-libav --exclude=gstreamer1-plugins-bad-free-devel ffmpeg gstreamer-ffmpeg`

## Accelerated Hardware  Codec
* Intel(recent) using the rpmfusion-nonfree section
* `sudo dnf install intel-media-driver`

* Intel(older) using the rpmfusion-free section
* `sudo dnf install libva-intel-driver`

# Hardware codecs with AMD (mesa)
* Using the rpmfusion-free section This is required since Fedora 37 and later. The following drivers mainly concern AMD hardware since NVIDIA hardware with nouveau doesnt work well
* `sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld`
* `sudo dnf swap mesa-vdpau-drivers mesa-vdpau-drivers-freeworld`

* If using i686 compat libraries (for steam or alikes)
* `sudo dnf swap mesa-va-drivers.i686 mesa-va-drivers-freeworld.i686`
* `sudo dnf swap mesa-vdpau-drivers.i686 mesa-vdpau-drivers-freeworld.i686`

# Hardware codec with NVIDIA
* The Nvidia proprieatary driver doesnt support VAAPI, but there is a wrapper that can bridge NVDEC/NVENC with VAAPI
* `sudo dnf install nvidia-vaapi-driver`

# Play DVDs
* Tainted repos requires the installation of the libdvdcss pkgs. Tainted free is dedicated for FLOSS packages where some usages might be restricted in some countries.
* `sudo dnf install libdvdcss`
* `sudo dnf install rpmfusion-free-release-tainted`

## Install additional codec
* This will allows the application using the gstreamer framework and other multimedia software, to play others restricted codecs.
* The following command will install the complements multimedia packages needed by gstreamer enabled applications
* `sudo dnf groupupdate multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin`

* Update and Install sound-and-video packages and core packages needed by some applications
* `sudo dnf groupupdate sound-and-video core`
* `sudo dnf group upgrade --with-optional Multimedia`

# Swap ffmpeg-free with rpmfusion ffmpeg
* Fedora ffmpeg-free works most of the time, but version missmatch can occur every now and again.
* You can switch to the rpmfusion provided ffmpeg build that is better supported.
* The next section is for additional codecs or plugins related to packages you might have or want to install.
* `sudo dnf swap ffmpeg-free ffmpeg --allowerasing`
* Add a little extra
* `sudo dnf install lame\* --exclude=lame-devel`

##  H/W Video Decoding w/ VA-API
* `sudo dnf install ffmpeg ffmpeg-libs libva libva-utils`

# Intel
* For Intel chipsets (5th Gen and above)
* `sudo dnf swap libva-intel-media-driver intel-media-driver --allowerasing`

# AMD
* If you have an AMD chipset, after installing the packages above do
* `sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld`

## OpenH264 for Firefox
* After this enable the OpenH264 Plugin in Firefoxs settings
* `sudo dnf config-manager --set-enabled fedora-#isco-openh264`
* `sudo dnf install -y openh264 gstreamer1-plugin-openh264 mozilla-openh264`

## System, Bios and Power

# Battery Life. Great for Labtops:
* `sudo dnf install tlp tlp-rdw`

* Mask Power Profiles Daemon
* `sudo systemctl mask power-profiles-daemon`

* and show battery percentage as true:
* `gsettings set org.gnome.desktop.interface show-battery-percentage true`

# Set UTC Time:
* Used to counter time inconsistencies in dual boot systems. If the clock in your bios is messed up this may help.
* `sudo timedatectl set-local-rtc '0'`

## Optimizations for that little extra boost:
* The tips below can allow you to squeeze out a little bit more performance from your system.
# Why disabling mitigations may or may not be for you.
* Increases performance in multithreaded systems. The more cores you have in your cpu the greater the performance gain. 
* 5-30% performance gain varying upon systems. Do not follow this if you share services and files through your network or are using fedora in a VM.
* Modern intel CPUs (above 10th gen) do not gain noticeable performance improvements upon disabling mitigations.
* Therefore, disabling mitigations can present some security risks against various attacks. However, it still might increase the CPU performance of your system.

# Disable Mitigations:
* `sudo grubby --update-kernel=ALL --args="mitigations=off"`

# Modern Standby:
* can result in better battery life when your laptop goes to sleep.
* `sudo grubby --update-kernel=ALL --args="mem_sleep_default=s2idle"`

* Note: If "s2idle" is not working, 

# Enable nvidia-modeset:
* Useful if you have a laptop with an Nvidia GPU. Necessary for some PRIME-related interoperability features.
* `sudo grubby --update-kernel=ALL --args="nvidia-drm.modeset=1"`

# Disable NetworkManager-wait-online.service:
* Disabling it can decrease the boot time by at least ~15s-20s:
* `sudo systemctl disable NetworkManager-wait-online.service`

# Disable Gnome Software from Startup Apps:
* Gnome software autostarts on boot for some reason, even though it is not required on every boot unless you want it to do updates in the background, this takes at least 100MB of RAM upto 900MB (as reported anecdotically).
* You can stop it from autostarting by:
* `sudo rm /etc/xdg/autostart/org.gnome.Software.desktop`

## Packages, Tools, Applications and Extensions

# Archive Tools for opening compressed files
* `sudo dnf install -y unzip p7zip p7zip-plugins unrar`

# GNOME Tweaks and Extension apps  
* If you haven’t installed Gnome Tweaks, its necessary to do so before proceeding.
* `sudo dnf install -y gnome-tweaks gnome-extensions-app`
                                  
# FlatHub, Flatpak repo
* `flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo`
                                  
# GNOME Extension Manager
* `flatpak install flathub com.mattjakeman.ExtensionManager`

# # Microsoft TrueType fonts
* Installing Microsoft fonts may help improve compatibility and/or stability of Microsoft Office generated files. (i.e. Microsoft 365.)
* `sudo dnf install curl cabextract xorg-x11-font-utils fontconfig`
* `sudo rpm -i https://downloads.sourceforge.net/project/mscorefonts2/rpms/msttcore-fonts-installer-2.6-1.noarch.rpm`

# Install and Make Nemo the default File Manager for GnomeDE
* Has good options for files & plugins.
* First we install
* `sudo dnf install -y nemo`
* Now we can apply the following to setup the directories and 
* `xdg-mime default nemo.desktop.inode/directory application/x-gnome-saved-search`
* Disable Nautilus
* `gsettings set org.gnome.desktop.background show-destop-icons false`
* Enable Nemo
* `gsettings set org.nemo.desktop show-destop-icons true`
* Thats it, done. Now you can check out some of the available plugins with a software manager like `dnfdragora` which also has a (-updater) app.

* Software management
* `sudo dnf install -y dnfdragora dnfdragora-updater`

###


## Theming [Optional]

### GTK Themes
* `gtk-murrine-engine` `breeze`
* `adw-gtk3-theme`
* Dont install the following if using a different spin of Fedora.
* https://github.com/lassekongo83/adw-gtk3
* https://github.com/vinceliuice/Colloid-gtk-theme
* https://github.com/EliverLara/Nordic
* https://github.com/vinceliuice/Orchis-theme
* https://github.com/vinceliuice/Graphite-gtk-theme

### Use themes in Flatpaks. 
* This will either enable or disable depending on prior setting. 
* `sudo flatpak override --filesystem=$HOME/.themes`
* `sudo flatpak override --env=GTK_THEME=my-theme` 

### Icon Packs
* https://github.com/vinceliuice/Tela-icon-theme
* https://github.com/vinceliuice/Colloid-gtk-theme/tree/main/icon-theme

### Wallpaper
* https://github.com/manishprivet/dynamic-gnome-wallpapers

### Firefox Theme
* Install Firefox Gnome theme by: `curl -s -o- https://raw.githubusercontent.com/rafaelmardojai/firefox-gnome-theme/master/scripts/install-by-curl.sh | bash`

### Starship (terminal theme)
* Also has a good assortment of Fonts for terminals
* Configure starship to make your terminal look good (refer https://starship.rs)

### Grub Theme
* grub2 theme like mac
* https://github.com/vinceliuice/grub2-themes

### Change the desktop eviroment.
*Fedora uses Gnome by default with several other desktop environment (DE) spins available. These DEs are still available after installing Fedora. Installing multiple DEs can cause conflicts with key managers and themes. Available desktop environments can be found with this command:
* `dnf grouplist -v`

### The command will yield a list similar to this:

   * Fedora Custom Operating System (custom-environment)
   * Minimal Install (minimal-environment)
   * Fedora Server Edition (server-product-environment)
   * Fedora Workstation (workstation-product-environment)
   * Fedora Cloud Server (cloud-server-environment)
   * KDE Plasma Workspaces (kde-desktop)
   * Xfce Desktop (xfce-desktop)
   * LXDE Desktop (lxde-desktop)
   * LXQt Desktop (lxqt-desktop)
   * Cinnamon Desktop (cinnamon-desktop)
   * MATE Desktop (mate-desktop)
   * Sugar Desktop Environment (sugar-desktop)
   * Deepin Desktop (deepin-desktop)
   * Development and Creative Workstation (developer-workstation)
   * Web Server (web-server-environment)
   * Infrastructure Server (infrastructure-server-environment)
   * Basic Desktop (basic-desktop-environment)

### Using the package name from the list above, install the desktop enviroment with a simple dnf install substituting kde-desktop-environment with the preferred DE.
* `sudo dnf install @kde-desktop`
* DEs can be switched at the login screen or alternatively, with the Desktop Switcher tool.
* `sudo dnf -y install switchdesk switchdesk-gui`


