```bash
# 1. Check internet
ping archlinux.org

# 2. Set keyboard (optional)
loadkeys us

# 3. Open disk tool
cfdisk /dev/sdX  # replace X with your drive letter

# 4. Format partitions
mkfs.fat -F32 /dev/sdX1        # EFI
mkfs.ext4 /dev/sdX2            # Root
mkswap /dev/sdX3               # Swap
swapon /dev/sdX3

# 5. Mount partitions
mount /dev/sdX2 /mnt
mkdir /mnt/boot
mount /dev/sdX1 /mnt/boot

# 6. Install base packages
pacstrap /mnt base linux linux-firmware sudo networkmanager

# 7. Generate fstab
genfstab -U /mnt >> /mnt/etc/fstab

# 8. Change root into system
arch-chroot /mnt

# 9. Set timezone
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
hwclock --systohc

# 10. Locale
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf

# 11. Hostname
echo "archi3" > /etc/hostname
nano /etc/hosts  # Add:
# 127.0.0.1   localhost
# ::1         localhost
# 127.0.1.1   archi3.localdomain archi3

# 12. Set root password
passwd

bootctl install

# 13. Create boot entry
nano /boot/loader/entries/arch.conf
# Add:
# title   Arch Linux
# linux   /vmlinuz-linux
# initrd  /initramfs-linux.img
# options root=/dev/sdX2 rw

echo "default arch" > /boot/loader/loader.conf

# 14. Add user
useradd -m -G wheel -s /bin/bash yourname
passwd yourname
EDITOR=nano visudo
# Uncomment: %wheel ALL=(ALL:ALL) ALL

# 15. Install packages
pacman -S xorg xorg-xinit i3 xterm lightdm lightdm-gtk-greeter firefox vim neofetch htop

# 16. Enable display manager
systemctl enable lightdm

# 17. Exit & unmount
exit
umount -R /mnt
reboot
```
