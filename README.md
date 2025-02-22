![Arch Linux Artwork](https://archlinux.org/static/logos/archlinux-logo-light-90dpi.d36c53534a2b.png)

# Instalación
`Arch Linux Install medium (x86_64, BIOS)`

```bash
loadkeys es
```

```bash
ping -c 1 google.com
```

## Particionado
- `cfdisk`
  - `gpt`
    - `New`
      - `512M`
      - `Type`
        - `EFI System`
    - `New`
      - `95G`
        - `Type`
          - `Linux filesystem`
    - `New`
      - `4.5G`
        - `Type`
          - `Linux swap`
    - `Write`
      - `yes`
    - `Quit`
- `lsblk`

## Formateo
```bash
mkfs.fat -F32 /dev/sda1
```
```bash
mkfs.ext4 /dev/sda2
```
```bash
mkswap /dev/sda3
```
```bash
swapon /dev/sda3
```

## Montado
```bash
mount /dev/sda2 /mnt
```
```bash
mkdir -p /mnt/boot/efi
```
```bash
mount /dev/sda1 /mnt/boot/efi
```
```bash
lsblk
```

## Instalar sistema base

```bash
pacstrap /mnt linux linux-firmware base base-devel networkmanager wpa_supplicant grub git vim sudo nano
```
  - **pacstrap**: Script de Arch Linux que usa pacman (el gestor de paquetes de Arch) para instalar paquetes en un directorio específico.
  - `/mnt`: Indica el directorio donde se instalarán los paquetes (la instalación de Arch montada).
  - **base**: Paquetes esenciales para que el sistema funcione.
  - **base-devel**: Grupo de paquetes para compilación (incluye make, gcc, pacman, etc.), útil para compilar software desde AUR o código fuente.
  - **linux**: Instala el kernel de Linux.
  - **linux-firmware**: Drivers y firmware para hardware de red, gráficos, WiFi, etc.
  - **networkmanager**: Servicio que gestiona conexiones de red de manera automática.
  - **wpa_supplicant**: Necesario para manejar redes WiFi en Linux.
  - **grub**: Bootloader que permite iniciar el sistema.
  - **sudo**: Necesario para gestionar permisos sin usar root directamente.
  - **git**: Fundamental para clonar repositorios de herramientas de hacking.
  - **vim**: Editor de texto potente que puede ser útil en pentesting.

## Generar archivo fstab

> Asegura que las particiones se monten automáticamente al arrancar el sistema.

```bash
genfstab -U /mnt > /mnt/etc/fstab
cat /mnt/etc/fstab
```

## Cambiar el entorno a la instalación nueva en `/mnt`

> Te coloca dentro del sistema recién instalado, como si ya hubieras iniciado en él. A partir de ahora, todos los comandos afectan a la instalación y no al sistema live de Arch.

```bash
arch-chroot /mnt
```

## Configurar zona horaria

> Establecé la zona horaria.

```bash
ln -sf /usr/share/zoneinfo/America/Argentina/Buenos_Aires /etc/localtime
```

> Sincronizá la hora.

```bash
hwclock --systohc
```

## Configurar el idioma y la localización

> Editá el archivo de configuración de locales.

```bash
nano /etc/locale.gen
```

> Buscá y descomentá (#) estas líneas:

```bash
en_US.UTF-8 UTF-8
es_AR.UTF-8 UTF-8
```

> Generá los locales:

```bash
locale-gen
```

> Creá el archivo de configuración de idioma predeterminado:

```bash
echo "LANG=es_AR.UTF-8" > /etc/locale.conf
```

> Configurá el teclado en español para la terminal:

```bash
echo "KEYMAP=es" > /etc/vconsole.conf
```

## Configurar el nombre del equipo (hostname)

> Definí el nombre de tu máquina:

```bash
echo "arch" > /etc/hostname
```

> Agregalo al archivo `/etc/hosts`

```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   arch.localdomain  arch
```

## Configurar la red (habilitar NetworkManager)

> Activá NetworkManager para gestionar conexiones automáticamente:

```bash
systemctl enable NetworkManager
```

## Crear usuario y configurar sudo

> Creá tu usuario

```bash
useradd -mG wheel -s /bin/bash arch
```

> Asignale una contraseña

```bash
passwd arch
```

> Permití que los usuarios del grupo `wheel` usen `sudo`

```bash
nano /etc/sudoers
```

> Buscá esta línea y descomentala (sacá el #):

```bash
%wheel ALL=(ALL:ALL) ALL
```

## Instalar y configurar el bootloader (GRUB)

> Instalá GRUB:

```bash
pacman -S grub efibootmgr
```

> Instalalo en el disco (/dev/sda):

```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
```

## Bootloader
- `grub-install /dev/sda`
- `grub-mkconfig -o /boot/grub/grub.cfg`

### Conexión a internet
- `sudo systemctl start wpa_supplicant.service`
- `sudo systemctl enable wpa_supplicant.service`
- `reboot now`

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
