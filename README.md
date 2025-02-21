![Arch Linux Artwork](https://archlinux.org/static/logos/archlinux-logo-light-90dpi.d36c53534a2b.png)

# Instalación

`Arch Linux Install medium (x86_64, BIOS)`

`loadkeys es`

`ping -c 1 google.com`

## Particionado

- `cfdisk`
  - `dos`
  - `New`
    - `512M`
    - `primary`
  - `New`
    - `95G`
    - `primary`
  - `New`
    - `4.5G`
    - `primary`
    - `Type`
      - `82 Linux swap / Solaris`
  - `Write`
    - `yes`
  - `Quit`
- `lsblk`

## Formateo

- `mkfs.vfat -F 32 /dev/sda1`
- `mkfs.ext4 /dev/sda2`
- `mkswap /dev/sda3`
- `swapon`

- `mount /dev/sda2 /mnt`
- `mkdir /mnt/boot`
- `mount /dev/sda1 /mnt/boot`

- `pacstrap /mnt linux linux-firmware networkmanager grub wpa_supplicant base base-devel`
- `genfstab -U /mnt > /mnt/etc/fstab`
- `arch-chroot /mnt`
- `passwd`
- `useradd -m arch`
- `passwd arch`
- `usermod -aG wheel arch`
- `pacman -S sudo vim nano`
- `nano /etc/sudoers`
- Descomentar la línea `%wheel ALL=(ALL:ALL) ALL`
- `nano /etc/locale.gen`
- `ctrl + w` `en_US` Descomentar la línea
- `ctrl + w` `es_AR` Descomentar la línea
- `locale-gen`
- `nano /etc/vconsole.conf`
  - KEYMAP=es
 
## Bootloader

- `grub-install /dev/sda`
- `grub-mkconfig -o /boot/grub/grub.cfg`

- `echo arch > /etc/hostname`
- `nano /etc/hosts`
  - 127.0.0.1 localhost arch.local arch
  - ::1 localhost
- `pacman -S neofetch`
- `reboot now`
- `sudo systemctl start NetworkManager.service`
- `sudo systemctl enable NetworkManager.service`
- `sudo systemctl start wpa_supplicant.service`
- `sudo systemctl enable wpa_supplicant.service`
- `pacman -S git`
- `mkdir -p Desktop/arch/repos`
- `cd Desktop/arch/repos`
- `git clone https://aur.archlinux.org/paru-bin.git`
- `cd paru-bin`
- `makepkg -si`
- `cd ~/Desktop/arch/repos`
- `mkdir blackarch`
- `curl -O https://blackarch.org/strap.sh`
- `chmod +x strap.sh`
- `sudo su`
- `./strap.sh`
- 
