 # Fedora 40 Post Install Guide
Things to do after installing Fedora 39

Post Installation Upgrades, Applications and more. This list is wriiten for Fedora Workststion but has something for everyone running Fedora.
## Set your hostname CLi:
`hostnamectl set-hostname YOUR.HOSTNAME.com`

## Faster DNF
* `sudo nano /etc/dnf/dnf.conf`
* Copy and add the following to your DNF configuration file:
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

## RPM Fusion:
* First enable access to the free and non-free repositories. If installing both the Mirror will be from the rpmfusion webpage.
* free
`https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-40.noarch.rpm`
* non-free
`https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-40.noarch.rpm`
* Mirror
`sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm`

## RPM Firmware:sudo dnf install -y gnome-tweaks gnome-extensions-app
* If you would like the Tainted repositories add the following
`sudo dnf install rpmfusion-nonfree-release-tainted`
`sudo dnf --repo=rpmfusion-nonfree-tainted install "*-firmware"`

## Update and Restart Now:
* DNF update is similar to Debian `sudo apt-get update && sudo apt-get upgrade`
`sudo dnf update -y`
`sudo dnf -y upgrade --refresh`

## Media Codec:
* It is Not Advised to cut or edit the following commands due to the possible resulting broken packages and --fix-broken ,, --allowerasing W: error flags. Please use the Full Syntax:
`sudo dnf groupupdate 'core' 'multimedia' 'sound-and-video' --setopt='install_weak_deps=False' --exclude='PackageKit-gstreamer-plugin' --allowerasing && sync`
`sudo dnf install gstreamer1-plugins-{bad-\*,good-\*,base} gstreamer1-plugin-openh264 gstreamer1-libav --exclude=gstreamer1-plugins-bad-free-devel ffmpeg gstreamer-ffmpeg`

## Hardware Accelerated Codec:
* Intel(recent) using the rpmfusion-nonfree section:
`sudo dnf install intel-media-driver`

* Intel(older) using the rpmfusion-free section :::
`sudo dnf install libva-intel-driver`

# Hardware codecs with AMD (mesa):
* Using the rpmfusion-free section This is required since Fedora 37 and later. The following drivers mainly concern AMD hardware since NVIDIA hardware with nouveau doesnt work well
`sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld`
`sudo dnf swap mesa-vdpau-drivers mesa-vdpau-drivers-freeworld`

* If using i686 compat libraries (for steam or alikes):
`sudo dnf swap mesa-va-drivers.i686 mesa-va-drivers-freeworld.i686`
`sudo dnf swap mesa-vdpau-drivers.i686 mesa-vdpau-drivers-freeworld.i686`

# Hardware codec with NVIDIA
* The Nvidia proprieatary driver doesnt support VAAPI, but there is a wrapper that can bridge NVDEC/NVENC with VAAPI
`sudo dnf install nvidia-vaapi-driver`

# Play DVDs:
* Tainted repos requires the installation of the libdvdcss pkgs. Tainted free is dedicated for FLOSS packages where some usages might be restricted in some countries.
`sudo dnf install libdvdcss`
`sudo dnf install rpmfusion-free-release-tainted`

## Install additional codec:
* This will allows the application using the gstreamer framework and other multimedia software, to play others restricted codecs.
* The following command will install the complements multimedia packages needed by gstreamer enabled applications:
`sudo dnf groupupdate multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin`

* The following command will install the sound-and-video complement packages as well as core packages needed by some applications:
`sudo dnf groupupdate sound-and-video core`

# Swap ffmpeg-free with rpmfusion ffmpeg:
* Fedora ffmpeg-free works most of the time, but version missmatch can occur every now and again. You can switch to the rpmfusion provided ffmpeg build that is better supported. You will still need to follow the next section for additional codecs or plugins related to packages you might have installed.
`sudo dnf swap ffmpeg-free ffmpeg --allowerasing`
`sudo dnf install lame\* --exclude=lame-devel`
`sudo dnf group upgrade --with-optional Multimedia`

##  H/W Video Decoding with VA-API:
`sudo dnf install ffmpeg ffmpeg-libs libva libva-utils`

# Intel:
* If you have a recent Intel chipset (5th Gen and above) after installing the packages above., Do:
`sudo dnf swap libva-intel-media-driver intel-media-driver --allowerasing`

* No need to do this for intel integrated graphics. Mesa drivers are for AMD graphics, who lost support for h264/h245 in the fedora repositories in f38 due to legal concerns.

# AMD:
* If you have an AMD chipset, after installing the packages above do:
`sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld`

## OpenH264 for Firefox:
* After this enable the OpenH264 Plugin in Firefoxs settings:
`sudo dnf config-manager --set-enabled fedora-#isco-openh264`
`sudo dnf install -y openh264 gstreamer1-plugin-openh264 mozilla-openh264`

## Update Flatpak:
`flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`

## Custom DNS Servers
* For people that want to setup custom DNS servers for better privacy
```
sudo mkdir -p '/etc/systemd/resolved.conf.d' && sudo -e '/etc/systemd/resolved.conf.d/99-dns-over-tls.conf'

[Resolve]
DNS=1.1.1.2#security.cloudflare-dns.com 1.0.0.2#security.cloudflare-dns.com 2606:4700:4700::1112#security.cloudflare-dns.com 2606:4700:4700::1002#security.cloudflare-dns.com
DNSOverTLS=yes
```



