Here’s a **step-by-step, first-person guide** to installing Arch Linux with i3, written as if you’re executing the commands yourself.  

---

# **Arch Linux + i3 WM Installation Guide**  
*(UEFI/GPT, Wi-Fi/Ethernet, Minimal Setup)*  

### **🔸 Boot into Live Environment**  
1. **Download ISO**: Get the latest Arch ISO from [archlinux.org](https://archlinux.org/download/).  
2. **Create USB**:  
   ```bash
   dd if=archlinux-2024.07.01-x86_64.iso of=/dev/sdX bs=4M status=progress
   ```  
   *(Replace `/dev/sdX` with your USB, e.g., `/dev/sdb`)*  
3. **Boot from USB**:  
   - Reboot → Enter BIOS (usually `F2`/`Del`).  
   - Disable **Secure Boot**, enable **UEFI**.  
   - Select USB as boot device.  

---

### **🔸 Connect to the Internet**  
#### **Wired (Ethernet)**:  
```bash
ping archlinux.org  # Check connection
```  

#### **Wi-Fi**:  
```bash
iwctl  
station wlan0 scan  
station wlan0 connect "Your-WiFi"  
exit  
ping archlinux.org  # Verify
```  

---

### **🔸 Partitioning (UEFI/GPT)**  
1. **List Disks**:  
   ```bash
   fdisk -l
   ```  
   *(Note your target disk, e.g., `/dev/nvme0n1`)*  

2. **Partition**:  
   ```bash
   cfdisk /dev/nvme0n1
   ```  
   - Create:  
     - **512MB** → Type `EFI System` (for `/boot`).  
     - **20GB+** → Type `Linux filesystem` (for `/`).  
     - *(Optional)* Swap partition (or use `zram` later).  

3. **Format**:  
   ```bash
   mkfs.fat -F32 /dev/nvme0n1p1  # EFI  
   mkfs.ext4 /dev/nvme0n1p2       # Root  
   mkswap /dev/nvme0n1p3          # Swap (if created)  
   swapon /dev/nvme0n1p3  
   ```  

4. **Mount**:  
   ```bash
   mount /dev/nvme0n1p2 /mnt  
   mkdir -p /mnt/boot  
   mount /dev/nvme0n1p1 /mnt/boot  
   ```  

---

### **🔸 Install Base System**  
```bash
pacstrap /mnt base linux linux-firmware nano sudo networkmanager grub efibootmgr
```  

Generate `fstab`:  
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```  

---

### **🔸 Chroot & Configure**  
1. **Enter Chroot**:  
   ```bash
   arch-chroot /mnt
   ```  

2. **Timezone & Locale**:  
   ```bash
   ln -sf /usr/share/zoneinfo/Europe/Kyiv /etc/localtime  
   hwclock --systohc  
   nano /etc/locale.gen  # Uncomment `en_US.UTF-8 UTF-8`  
   locale-gen  
   echo "LANG=en_US.UTF-8" > /etc/locale.conf  
   ```  

3. **Hostname & Users**:  
   ```bash
   echo "arch-i3" > /etc/hostname  
   passwd                  # Set root password  
   useradd -m -G wheel -s /bin/bash yourname  
   passwd yourname  
   EDITOR=nano visudo      # Uncomment `%wheel ALL=(ALL) ALL`  
   ```  

4. **Bootloader (GRUB)**:  
   ```bash
   grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB  
   grub-mkconfig -o /boot/grub/grub.cfg  
   ```  

5. **Enable Services**:  
   ```bash
   systemctl enable NetworkManager  
   systemctl enable sshd  # Optional  
   ```  

6. **Exit & Reboot**:  
   ```bash
   exit  
   umount -R /mnt  
   reboot  
   ```  

---

### **🔸 Install i3 & Desktop Environment**  
1. **Login** as your user.  

2. **Install Xorg + i3**:  
   ```bash
   sudo pacman -S xorg xorg-xinit i3-gaps dmenu alacritty feh picom  
   ```  

3. **Start i3**:  
   ```bash
   startx
   ```  
   - i3 will ask to generate a config. Press `Enter` to confirm.  
   - Set `Win` (Mod) key as default.  

4. **Autostart Apps**: Edit `~/.config/i3/config`:  
   ```bash
   exec --no-startup-id nm-applet  
   exec --no-startup-id picom --daemon  
   exec --no-startup-id feh --bg-scale ~/wallpaper.jpg  
   ```  

5. **Optional Tools**:  
   ```bash
   sudo pacman -S firefox ranger neovim pulseaudio  
   ```  

---

### **🔸 First i3 Session**  
- **Open Terminal**: `Win + Enter` (default keybind).  
- **Launch Apps**: `Win + d` (dmenu).  
- **Reload Config**: `Win + Shift + r`.  

---

### **✅ Done!**  
Your **Arch Linux + i3** setup is complete. Customize further with:  
- **Polybar** (status bar)  
- **Rofi** (app launcher)  
- **Nitrogen** (wallpaper manager)  

For NVIDIA drivers:  
```bash
sudo pacman -S nvidia nvidia-utils  
```  

Need help? Check the [Arch Wiki](https://wiki.archlinux.org/). 🚀
