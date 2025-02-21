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
- `genfstab -U /mnt`
