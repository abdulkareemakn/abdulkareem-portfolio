---
title: How to install Arch Linux
description: A comprehensive Arch Linux installation guide configured how I like it.
pubDate: "2026-04-26"   
heroImage: "/post_img.webp"
tags: ["arch"]
---

<!-- ## Table of Contents -->


### Introduction
This is an opinionated Arch Linux installation guide designed according to my preferences. I want to preface this guide by stating that the official Arch Linux [installation guide](https://wiki.archlinux.org/title/Installation_guide) is amazing and this guide derives directly from that. This is specifically written according to my preferences and also expands on the installation guide by doing some basic configuration of the system such as creating the first user and installing packages.


This guide also assumes that you have already booted into the live archiso and are running on a UEFI system. Let's start.

#### Setting keyboard layout and font
```sh
loadkeys us
setfont ter-132b
```

#### Connect to the internet
```sh
iwctl
```

    [iwd]# station wlan0 scan
    [iwd]# station wlan0 connect <network-name>
    [iwd]# exit

##### Verify the connection
```sh
ping -c 5 archlinux.org
```


#### Partitioning and Formatting 
I like to use the `cfdisk` for partitioning my disk. It's an interactive terminal interface. I prefer the following partition layout for my disk.

##### Disk Layout
```sh
NAME        SIZE    FS
/dev/sda1   1G      FAT32
/dev/sda2   20G     [SWAP]
/dev/sda3   455G    ext4
```

```sh
mkfs.fat -F 32 /dev/sda1
mkfs.ext4 /dev/sda3
mkswap /dev/sda2
```

I like to have both root and home on one partition. I also prefer a dedicated swap partition.

#### Mounting the file system
```sh
mount /dev/sda3 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
swapon /dev/sda2
```

#### Updaing pacman mirrors
```sh
reflector --country Singapore,China,  --age 24 --protocol https --sort rate --fastest 5 --save /etc/pacman.d/mirrorlist
```

Note the extra comma after China and a blank space. That enables global mirrors which includes the Fastly CDN based arch linux mirror.


#### Installing the base system
```sh
pacstrap -K /mnt base base-devel linux linux-firmware sof-firmware intel-ucode grub efibootmgr networkmanager neovim man-db git zsh docker gdm hyprland kitty
```

#### Generate fstab
```sh
genfstab -U /mnt >> /mnt/etc/fstab
```

#### Configuring the system
```sh
arch-chroot /mnt
```

#### Timezone
```sh
ln -sf /usr/share/zoneinfo/Asia/Karachi /etc/localtime
hwclock --systohc
systemctl enable systemd-timesyncd
```

#### Localization
```sh
echo "en_US.UTF-8 UTF-8" >>/etc/locale.gen
locale-gen

echo "LANG=en_US.UTF-8" >/etc/locale.conf
echo "KEYMAP=us" >/etc/vconsole.conf
```

#### Hostname
```sh
echo bytecave > /etc/hostname
```

#### Bootloader
```sh
grub-install
grub-mkconfig -o /boot/grub/grub.cfg
```

#### Enabling Services
```sh
systemctl enable NetworkManager
systemctl enable bluetooth
systemctl enable fstrim.timer
systemctl enable gdm
```

#### Account Setup

##### Password for `root` account
```sh
passwd
```

#### Creating a new user
```sh
useradd -m -G wheel docker -s $(which zsh) username
passwd username
```

#### Reboot
At this point, I like to reboot my system and switch to the proper installation on the disk.
```sh
exit
systemctl reboot
```

After the reboot, I use gdm to login to my hyprland session where the remaining configuration continues.

#### Installing an AUR helper
My preferred AUR helper is `yay`.

```sh
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
cd ..
rm -rf yay
```


#### Installing my packages
```sh
git clone https://github.com/abdulkareemakn/dotfiles.git
cd dotfiles
yay -Syu --needed --noconfirm - < pkglist
```

#### Configuring my applications
From the previous step, I have already cloned my dotfiles repository from GitHub and will now apply the configuration to all the applications I use.
```sh
stow bat btop dunst eza hyprland kitty lazygit nvim oh-my-posh rofi waybar yazi zsh
```

### Conclusion
That's it. My Arch Linux system is now ready and configured and I can begin using it. I hope this guide was useful for you and provides an insight into an opinionated Arch Linux install. Thanks for reading. Goodbye.
