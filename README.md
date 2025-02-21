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

## Montado
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

### Hostname
- `echo arch > /etc/hostname`

### Hosts  
- `nano /etc/hosts`
  - 127.0.0.1 localhost arch.local arch
  - ::1 localhost

### Conexión a internet
- `sudo systemctl start NetworkManager.service`
- `sudo systemctl enable NetworkManager.service`
- `sudo systemctl start wpa_supplicant.service`
- `sudo systemctl enable wpa_supplicant.service`
- `reboot now`

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
  
- `pacman -S xorg xorg-server`
- `pacman -S gnome`
- `systemctl start gdm.service`
- `systemctl enable gdm.service`
- `pacman -S kitty`
- `reboot now`

### VMWare tools
- `pacman -S gtkmm open-vm-tools xf86-video-vmware xf86-input-vmmouse`
- `systemctl enable vmtoolsd`

- En GNOME ingresar a la configuración del teclado y agregar español
- En la **kitty** `pacman -S firefox`
- `pacman -S wget p7zip zsh`
- `sudo usermod --shell /usr/bin/zsh arch`
- `localectl set-xll-keymap es`
- `paru -S zsh-syntax-highlighting zsh-autosuggestions`
- `sudo pacman -S locate`
- `sudo updatedb`
- `sudo pacman -S lsd bat mdcat`
- `paru -S scrub`
