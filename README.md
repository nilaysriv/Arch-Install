# Arch-Install
# Everything here is for my own use, be careful if you don't know what you are doing.
# This is for a UEFI install only.

# Connect Wifi
iwctl

station wlan0 connect <Network name>
	
exit


# Disk Wipe --This will wipe all the data from your disk
gdisk /dev/nvme0n1

x

z

y

y


# Disk Partition
cfdisk /dev/nvme0n1
# 128M for EFI, 8G for swap, rest for Linux install

# Filesystem set-up
mkfs.vfat -F32 /dev/nvme0n1p1

mkswap /dev/nvme0n1p2

swapon /dev/nvme0n1p2

mkfs.btrfs /dev/nvme0n1p3


# Mount 
mount /dev/nvme0n1p3 /mnt

btrfs su cr /mnt/@

umount /mnt

mount -o compress=lzo,subvol=@ /dev/nvme0n1p3 /mnt

mkdir -p /mnt/boot/EFI

mount /dev/nvme0n1p1 /mnt/boot/EFI


# Base Installation
pacstrap /mnt base base-devel grub-efi-x86_64 efibootmgr os-prober intel-ucode linux linux-headers linux-firmware zsh zsh-completions wpa_supplicant iwd dialog usbutils nvme-cli nano

# Generate fstab
genfstab -pU /mnt >> /mnt/etc/fstab

# Chroot
arch-chroot /mnt /bin/bash

# Grub Install
pacman -S grub-btrfs

grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB

grub-mkconfig -o /boot/grub/grub.cfg


# Check efi entries
efibootmgr

# Set Locale to en_IN.UTF-8
nano /etc/locale.gen
// uncomment en_IN.UTF-8 and en_US.UTF-8

locale-gen

echo LANG=en_IN.UTF-8 > /etc/locale.conf

export LANG=en_IN.UTF-8

# Timezone

ls /usr/share/zoneinfo/

ln -sf /usr/share/zoneinfo/Asia/Kolkata > /etc/localtime

timedatectl list-timezones
timedatectl set-timezone Asia/Kolkata

hwclock --systohc


# Set Hostname (hostnamehere is the hostname)
echo hostnamehere > /etc/hostname

# Enable ILoveCandy + Colour support in pacman
nano /etc/pacman.conf 	// uncomment "#Color" and add "ILoveCandy" in the misc options part on a new line below "#VerbosePkgLists"

# Enable multilib for 32bit applications
nano /etc/pacman.conf 	// uncomment "#[multilib]" and "#Include = /etc/pacman.d/mirrorlist"

pacman -Syu

# Setup Users (username is the user, change to the name that is required)
passwd

useradd -m -g users -G wheel,uucp -s /bin/zsh username

passwd username


# setup sudo for group "wheel" using the root password - must use "EDITOR=nano visudo" command
EDITOR=nano visudo 	//Search for "#%wheel ALL=(ALL) ALL" and uncomment that line and add this line "Defaults rootpw" below so it

# Desktop Environment
pacman -S xorg-server lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings xfce4 xfce4-goodies networkmanager gufw bluez alsa-utils bluez-utils pulseaudio-bluetooth ttf-dejavu ttf-opensans ttf-liberation noto-fonts noto-fonts-emoji ntfs-3g gnome-disk-utility gnome-system-monitor reflector neofetch blueman xorg-xrandr

# Enable services and boot to system
systemctl enable fstrim.timer

systemctl enable lightdm

systemctl enable NetworkManager

systemctl enable ufw

systemctl enable bluetooth

systemctl mask hibernate.target

exit

reboot


# Rank Mirrors
sudo reflector -c India

# Install NVIDIA drivers
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils vulkan-icd-loader nvidia-settings

# Set Swappiness
sudo gedit /etc/sysctl.d/99-swappiness.conf

vm.swappiness=10

# Install git + yay
sudo pacman -S git

git clone https://aur.archlinux.org/yay.git

cd yay

makepkg -si


# Install OPTIMUS for NVIDIA+Intel UHD Laptops
yay -S optimus-manager optimus-manager-qt

# Install important packages
yay -S visual-studio-code-bin libreoffice-fresh gedit vlc telegram-desktop gpart mtools f2fs-tools ntfs-3g exfatprogs udftools jdk-openjdk grub-customizer numix-gtk-theme gvfs-smb gvfs-mtp libmtp android-udev android-tools mate-calc google-chrome skype spotify discord neofetch

# Install gnome screenshot tool
yay -S gnome-screenshot

mkdir -p /home/username/Pictures/Screenshots

gsettings set org.gnome.gnome-screenshot auto-save-directory file:///home/username/Pictures/Screenshots


# Packages for Building ANDROID
yay -S multilib-devel ccache maven gradle gcc repo gnupg gperf sdl wxgtk2 squashfs-tools curl ncurses zlib schedtool perl-switch zip unzip libxslt bc rsync ccache lib32-zlib lib32-ncurses lib32-readline ncurses5-compat-libs lib32-ncurses5-compat-libs lib32-gcc-libs gnupg flex bison gperf sdl wxgtk2 squashfs-tools curl ncurses zlib schedtool perl-switch libxslt python2-virtualenv bc rsync lib32-zlib lib32-ncurses lib32-readline xml2 lzop pngcrush imagemagick

# Full Colour Range
xrandr --output HDMI-1 --set "Broadcast RGB" "Full"

# Nuke GnuPG
sudo rm -fr /etc/pacman.d/gnupg

sudo pacman-key --init

sudo pacman-key --populate archlinux

sudo pacman-key --refresh-keys

sudo pacman -Syu


# Trim
fstrim -v /

# Gaming on LINUX
yay -S lutris steam wine-staging giflib lib32-giflib libpng lib32-libpng libldap lib32-libldap gnutls lib32-gnutls mpg123 lib32-mpg123 openal lib32-openal v4l-utils lib32-v4l-utils libpulse lib32-libpulse libgpg-error lib32-libgpg-error alsa-plugins lib32-alsa-plugins alsa-lib lib32-alsa-lib libjpeg-turbo lib32-libjpeg-turbo sqlite lib32-sqlite libxcomposite lib32-libxcomposite libxinerama lib32-libgcrypt libgcrypt lib32-libxinerama ncurses lib32-ncurses opencl-icd-loader lib32-opencl-icd-loader libxslt lib32-libxslt libva lib32-libva gtk3 lib32-gtk3 gst-plugins-base-libs lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader 

##### Complete #####
