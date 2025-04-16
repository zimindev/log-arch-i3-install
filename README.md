# 1. Check internet
ping archlinux.org
# PING archlinux.org (95.217.163.246) 56(84) bytes of data.
# 64 bytes from archlinux.org (95.217.163.246): icmp_seq=1 ttl=55 time=24.3 ms

# 2. Set keyboard layout (US)
loadkeys us

# 3. Sync time
timedatectl set-ntp true
# Checking NTP enabled: yes

# 4. Partition disk (using /dev/nvme0n1)
cfdisk /dev/nvme0n1
# Created:
# /dev/nvme0n1p1 512M EFI System
# /dev/nvme0n1p2 30G Linux filesystem
# /dev/nvme0n1p3 8G Linux swap

# 5. Format partitions
mkfs.fat -F32 /dev/nvme0n1p1
# mkfs.fat 4.2 (2021-01-31)
mkfs.ext4 /dev/nvme0n1p2
# mke2fs 1.46.5 (Feb 2022)
mkswap /dev/nvme0n1p3
# Setting up swapspace version 1, size = 8 GiB

# 6. Mount
mount /dev/nvme0n1p2 /mnt
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
swapon /dev/nvme0n1p3

# 7. Install base
pacstrap /mnt base linux linux-firmware vim networkmanager sudo
# [Package installation output...]

# 8. Generate fstab
genfstab -U /mnt >> /mnt/etc/fstab
# Generated fstab with UUIDs

# 9. Enter system
arch-chroot /mnt

# 10. Timezone & locale (Europe/London)
ln -sf /usr/share/zoneinfo/Europe/London /etc/localtime
hwclock --systohc
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
locale-gen
# Generating locales...
echo "LANG=en_US.UTF-8" > /etc/locale.conf

# 11. Hostname
echo "archi3" > /etc/hostname
nano /etc/hosts
# Added:
# 127.0.0.1   localhost
# ::1         localhost
# 127.0.1.1   archi3.localdomain archi3

# 12. Set root password
passwd
# New password: ********
# Retype new password: ********

# 13. Bootloader (systemd-boot)
bootctl install
# Created "/boot/EFI" directory
# Created "/boot/loader" directory
nano /boot/loader/entries/arch.conf
# Added:
# title   Arch Linux
# linux   /vmlinuz-linux
# initrd  /initramfs-linux.img
# options root=UUID=xxxx-xxxx-xxxx rw
echo "default arch" > /boot/loader/loader.conf

# 14. Enable network
systemctl enable NetworkManager
# Created symlink /etc/systemd/system/multi-user.target.wants/NetworkManager.service

# 15. Add user
useradd -m -G wheel -s /bin/bash alex
passwd alex
# New password: ********
# Retype new password: ********
EDITOR=nano visudo
# Uncommented: %wheel ALL=(ALL:ALL) ALL

# 16. Install X and i3
pacman -S xorg xorg-xinit i3 xterm lightdm lightdm-gtk-greeter
# [Package installation output...]

# 17. Enable display manager
systemctl enable lightdm
# Created symlink /etc/systemd/system/display-manager.service → /usr/lib/systemd/system/lightdm.service

# 18. Finish
exit
umount -R /mnt
reboot
# System rebooting...
