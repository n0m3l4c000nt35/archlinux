![Arch Linux Artwork](https://archlinux.org/static/logos/archlinux-logo-light-90dpi.d36c53534a2b.png)

# Instalación

Modificar en las opciones avanzadas de la máquina virtual el tipo de firmware a `UEFI`

`Arch Linux Install medium (x86_64, BIOS)`

```bash
loadkeys la-latin1
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
pacstrap /mnt linux linux-firmware base base-devel gtkmm3 networkmanager wpa_supplicant grub git vim sudo nano
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
systemctl enable wpa_supplicant
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
grub-mkconfig -o /boot/grub/grub.cfg
```

```bash
exit
umount -R /mnt
reboot
```

```bash
sudo su
passwd
```

## Instalar Paru

```bash
git clone https://aur.archlinux.org/paru-bin.git
cd paru-bin
makepkg -si
```

## Agregar los repos de BlackArch

```bash
curl -O https://blackarch.org/strap.sh
chmod +x strap.sh
sudo ./strap.sh
rm strap.sh
# Actualizá la base de datos de pacman para incluir BlackArch
sudo pacman -Syyu
```
  
## Instalar xorg

> Servidor gráfico en Linux, permite tener una interfaz gráfica (GUI) en lugar de solo usar la consola

- **xorg**: Metapaquete que instala varios componentes esenciales de Xorg (drivers, utilidades, configuraciones).
- **xorg-server**: Es el servidor gráfico en sí, que se encarga de dibujar las ventanas en pantalla.

```bash
sudo pacman -S xorg xorg-xinit xorg-server
```

## VMWare tools

> Mejoran la integración de la VM con el host

```bash
sudo pacman -S open-vm-tools xf86-video-vmware xf86-input-vmmouse
systemctl enable vmtoolsd
```

## Instalar bspwm y sxhkd

```bash
sudo pacman -S bspwm sxhkd
mkdir -p ~/.config/bspwm ~/.config/sxhkd
cp /usr/share/doc/bspwm/examples/bspwmrc ~/.config/bspwm/
cp /usr/share/doc/bspwm/examples/sxhkdrc ~/.config/sxhkd/
chmod +x ~/.config/bspwm/bspwmrc
```

```bash
nano ~/.xinitrc
setxkbmap latam
exec bspwm
```

```bash
nano ~/.bash_profile
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && exec startx
```

### ESTO NO ESTA INSTALADO AUN ###

- `pacman -S wget p7zip zsh kitty zsh-syntax-highlighting zsh-autosuggestions locate lsd bat mdcat firefox xclip`
- `paru -S scrub`
- `sudo updatedb`
- `sudo usermod --shell /usr/bin/zsh arch`
- `localectl set-xll-keymap es`
