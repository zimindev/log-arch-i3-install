# log-arch-i3-install 14.04.2025
---
Here’s a **step-by-step guide** to installing **Arch Linux with i3 WM** (UEFI/GPT setup).  

---

## **📌 Pre-Installation**  
### **1. Download Arch Linux ISO**  
- Get the latest ISO from [archlinux.org](https://archlinux.org/download/)  
- Create a bootable USB:  
  ```bash
  dd if=archlinux.iso of=/dev/sdX bs=4M status=progress
  ```  
  *(Replace `/dev/sdX` with your USB drive, e.g., `/dev/sdb`)*  

### **2. Boot into Live Environment**  
- Restart PC, enter BIOS/UEFI (usually `Del`/`F2`/`F12`), and:  
  - Disable **Secure Boot**  
  - Enable **UEFI Mode**  
  - Select USB as boot device  

---

## **🛠️ Installation Steps**  

### **1. Connect to the Internet**  
**Wired (Ethernet):** Should work automatically.  
**Wi-Fi:** Use `iwctl`:  
```bash
iwctl                           # Enter interactive prompt
station wlan0 connect SSID      # Replace "SSID" with your network
exit
ping archlinux.org              # Test connection
```

### **2. Partitioning (UEFI/GPT)**  
- List disks:  
  ```bash
  fdisk -l
  ```  
- Partition (e.g., `/dev/nvme0n1`):  
  ```bash
  cfdisk /dev/nvme0n1           # Use arrow keys, create:
  ```
  - **512MB** → `EFI System` (Type `EF00`)  
  - **20GB+** → `Linux filesystem` (Root `/`)  
  - **(Optional)** Swap partition (if not using `zram`)  

### **3. Format Partitions**  
```bash
mkfs.fat -F32 /dev/nvme0n1p1    # EFI
mkfs.ext4 /dev/nvme0n1p2        # Root
mkswap /dev/nvme0n1p3           # Swap (if created)
swapon /dev/nvme0n1p3
```

### **4. Mount Partitions**  
```bash
mount /dev/nvme0n1p2 /mnt
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
```

### **5. Install Base System**  
```bash
pacstrap /mnt base linux linux-firmware nano sudo networkmanager grub efibootmgr
```

### **6. Generate fstab**  
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

### **7. Chroot into Installed System**  
```bash
arch-chroot /mnt
```

---

## **⚙️ Post-Installation Setup**  

### **1. Timezone & Locale**  
```bash
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime   # e.g., "Europe/Kyiv"
hwclock --systohc
nano /etc/locale.gen                                    # Uncomment `en_US.UTF-8 UTF-8`
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

### **2. Hostname & Users**  
```bash
echo "myarch" > /etc/hostname
passwd                                                 # Set root password
useradd -m -G wheel -s /bin/bash username
passwd username
EDITOR=nano visudo                                     # Uncomment `%wheel ALL=(ALL) ALL`
```

### **3. Bootloader (GRUB)**  
```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

### **4. Enable Essential Services**  
```bash
systemctl enable NetworkManager
systemctl enable sshd                                  # Optional
exit                                                  # Exit chroot
umount -R /mnt
reboot
```

---

## **🎨 Installing i3 Window Manager**  

### **1. Install Xorg & i3**  
```bash
sudo pacman -S xorg xorg-xinit i3-gaps dmenu alacritty feh picom
```

### **2. Configure i3**  
- Start Xorg:  
  ```bash
  startx
  ```  
- i3 will prompt to generate config (`~/.config/i3/config`).  

### **3. Autostart Apps (Edit `~/.config/i3/config`)**  
```bash
exec --no-startup-id nm-applet
exec --no-startup-id picom --daemon
exec --no-startup-id feh --bg-scale /path/to/wallpaper.jpg
```

### **4. Optional Tools**  
- **File Manager**: `ranger` or `thunar`  
- **Browser**: `firefox`  
- **Text Editor**: `neovim`  
- **Audio**: `pulseaudio`/`pipewire`  

---

## **✅ Final Steps**  
1. **Reboot**:  
   ```bash
   reboot
   ```  
2. **Login** and run:  
   ```bash
   startx
   ```  

---

### **💡 Tips**  
- Use `mod` (Windows key) + `Enter` to open terminal (`alacritty`).  
- `mod` + `d` launches `dmenu` for apps.  
- For NVIDIA drivers, see [Arch Wiki](https://wiki.archlinux.org/title/NVIDIA).  

Done! 🚀 Your **Arch + i3** system is ready. Customize further with `polybar`, `rofi`, etc.
