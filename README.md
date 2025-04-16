# Arch Linux + i3 Window Manager Installation (Simple Guide)

A basic and clean Arch Linux setup with the i3 window manager.

---

## Requirements

- Internet connection
- At least 15GB disk space
- UEFI or BIOS system

---

## Step-by-Step Installation

### 1. Boot from ISO

```bash
ping archlinux.org  # check internet
```

---

### 2. Set Keyboard (optional)

```bash
loadkeys ua  # example: Ukrainian layout
```

---

### 3. Enable Time Sync

```bash
timedatectl set-ntp true
```

---

### 4. Partition the Disk

```bash
cfdisk /dev/sdX  # choose GPT or DOS
```

Create:
- EFI (512M, type EFI System)
- Root (ext4)
- (Optional) Swap

---

### 5. Format Partitions

```bash
mkfs.fat -F32 /dev/sdX1     # EFI
mkfs.ext4 /dev/sdX2         # Root
mkswap /dev/sdX3            # Swap
```

---

### 6. Mount Partitions

```bash
mount /dev/sdX2 /mnt
mkdir /mnt/boot
mount /dev/sdX1 /mnt/boot
swapon /dev/sdX3
```

---

### 7. Install Base System

```bash
pacstrap /mnt base linux linux-firmware vim networkmanager sudo
```

---

### 8. Generate fstab

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

---

### 9. Enter New System

```bash
arch-chroot /mnt
```

---

### 10. Timezone and Locale

```bash
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
hwclock --systohc

echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
locale-gen

echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

---

### 11. Hostname

```bash
echo "archi3" > /etc/hostname

cat >> /etc/hosts << EOF
127.0.0.1   localhost
::1         localhost
127.0.1.1   archi3.localdomain archi3
EOF
```

---

### 12. Set Root Password

```bash
passwd
```

---

### 13. Bootloader (systemd-boot)

```bash
bootctl install

cat > /boot/loader/entries/arch.conf << EOF
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options root=/dev/sdX2 rw
EOF

echo "default arch" > /boot/loader/loader.conf
```

---

### 14. Enable Network

```bash
systemctl enable NetworkManager
```

---

### 15. Create User

```bash
useradd -m -G wheel -s /bin/bash yourname
passwd yourname

visudo  # uncomment: %wheel ALL=(ALL:ALL) ALL
```

---

### 16. Install X and i3

```bash
pacman -S xorg xorg-xinit i3 xterm lightdm lightdm-gtk-greeter
```

Enable display manager:

```bash
systemctl enable lightdm
```

---

### 17. Finish Install

```bash
exit
umount -R /mnt
reboot
```

---

### 18. First Boot

Login and enjoy your new i3 setup.

---

## Extra Tools (Optional)

```bash
pacman -S firefox thunar rofi dmenu neofetch htop picom feh lxappearance ttf-dejavu
```

---

## i3 Config Location

```bash
~/.config/i3/config
```

