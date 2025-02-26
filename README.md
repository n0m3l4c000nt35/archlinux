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
pacstrap /mnt linux linux-firmware base base-devel gtkmm3 networkmanager wpa_supplicant grub sudo git nano
```
  - **pacstrap**: Script de Arch Linux que usa pacman (el gestor de paquetes de Arch) para instalar paquetes en un directorio específico.
  - **/mnt**: Indica el directorio donde se instalarán los paquetes (la instalación de Arch montada).
  - **linux**: Instala el kernel de Linux.
  - **linux-firmware**: Drivers y firmware para hardware de red, gráficos, WiFi, etc.
  - **base**: Paquetes esenciales para que el sistema funcione.
  - **base-devel**: Grupo de paquetes para compilación (incluye make, gcc, pacman, etc.), útil para compilar software desde AUR o código fuente.
  - **gtkmm3**: ???
  - **networkmanager**: Servicio que gestiona conexiones de red de manera automática.
  - **wpa_supplicant**: Necesario para manejar redes WiFi en Linux.
  - **grub**: Bootloader que permite iniciar el sistema.
  - **sudo**: Necesario para gestionar permisos sin usar root directamente.
  - **git**: Fundamental para clonar repositorios.
  - **nano**: Editor de texto.

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

Establecer zona horaria

```bash
ln -sf /usr/share/zoneinfo/America/Argentina/Buenos_Aires /etc/localtime
```

Sincronizar hora

```bash
hwclock --systohc
```

## Configurar el idioma y la localización

Editar el archivo de configuración de locales

```bash
nano /etc/locale.gen
```

Descomentar estas líneas

```bash
en_US.UTF-8 UTF-8
es_AR.UTF-8 UTF-8
```

Generar locales

```bash
locale-gen
```

Crear archivo de configuración de idioma predeterminado

```bash
echo "LANG=es_AR.UTF-8" > /etc/locale.conf
```

Configurar teclado en español latinoamericano para la terminal

```bash
echo "KEYMAP=la-latin1" > /etc/vconsole.conf
```

## Configurar el nombre del equipo (hostname)

Definir nombre de la máquina

```bash
echo "arch" > /etc/hostname
```

Agregar al archivo `/etc/hosts`

```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   arch.localdomain  arch
```

## Configurar la red (habilitar NetworkManager)

Activar NetworkManager para gestionar conexiones automáticamente

```bash
systemctl enable NetworkManager
systemctl enable wpa_supplicant
```

## Crear usuario y configurar sudo

Crear usuario

```bash
useradd -mG wheel -s /bin/bash arch
```

Asignarle contraseña

```bash
passwd arch
```

Permitir que los usuarios del grupo `wheel` usen `sudo`

```bash
nano /etc/sudoers
```

Descomentar la siguiente línea

```bash
%wheel ALL=(ALL:ALL) ALL
```

## Instalar y configurar el bootloader (GRUB)

Instalar GRUB

```bash
pacman -S grub efibootmgr
```

Instalarlo en el disco (/dev/sda):

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
sudo pacman -S open-vm-tools xf86-video-vmware xf86-input-vmmouse mesa
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

Modificar las siguientes líneas del archivo `~/.config/sxhkd/sxhkdrc`

```bash
nano ~/.config/sxhkd/sxhkdrc
```

```bash
# quit/restart bspwm
super + shift + {q,r}
  bspc {quit,wm -r}

# focus the node in the given direction
super + {_,shift + }{Left,Down,Up,Right}
  bspc node -{f,s} {west,south,north,east}

# preselect the direction
super + ctrl + alt + {Left,Down,Up,Right}
  bspc node -p {west,south,north,east}

# move a floating window
super + alt + shift + {Left,Down,Up,Right}
  bspc node -v {-20 0,0 20,0 -20,20 0}

# custom resize
super + alt + {Left,Down,Up,Right}
  ~/.config/bspwm/scripts/bspwm_resize {west,south,north,east}
```

Eliminar las siguientes líneas del archivo `~/.config/sxhkd/sxhkdrc`

```bash
nano ~/.config/sxhkd/sxhkdrc
```

```bash
# expand a window by moving one of its side outward
super + alt + {h,j,k,l}
  bspc node -z {left -20 0,bottom 0 20,top 0 -20,right 20 0}

# contract a window by moving one of its side inward
super + alt + shift + {h,j,k,l}
  bspc node -z {right -20 0,top 0 20,bottom 0 -20,left 20 0}
```

Crear el script `~/.config/bspwm/scripts/bspwm_resize`

```bash
mkdir ~/.config/bspwm/scripts
touch ~/.config/bspwm/scripts/bspwm_resize
chmod +x ~/.config/bspwm/scripts/bspwm_resize
```

Agregarle el siguiente contenido al archivo `~/.config/bspwm/scripts/bspwm_resize`

```bash
nano ~/.config/bspwm/scripts/bspwm_resize
```

```bash
#!/usr/bin/env dash

if bspc query -N -n focused.floating > /dev/null; then
	step=20
else
	step=100
fi

case "$1" in
	west) dir=right; falldir=left; x="-$step"; y=0;;
	east) dir=right; falldir=left; x="$step"; y=0;;
	north) dir=top; falldir=bottom; x=0; y="-$step";;
	south) dir=top; falldir=bottom; x=0; y="$step";;
esac

bspc node -z "$dir" "$x" "$y" || bspc node -z "$falldir" "$x" "$y"
```

Para poder copiar de manera bidireccional entre la máquina host y la máquina virtual, agregar la siguiente línea al archivo `~/.config/bspwm/bspwmrc`

```bash
nano ~/.config/bspwm/bspwmrc
```

```bash
#! /bin/sh

pgrep -x sxhkd > /dev/null || sxhkd &

bspc monitor -d I II III IV V VI VII VIII IX X

bspc config borderless_monocle   true
bspc config gapless_monocle      true

bspc config split_ratio 0.5

bspc config window_gap 4
bspc config border_width 1
bspc config normal_border_color "#5d5d5d"
bspc config focused_border_color "#1A7A14"

vmware-user-suid-wrapper &
```

Eliminar las siguientes líneas del archivo `$HOME/.config/bspwm/bspwmrc`

```bash
bspc rule -a Gimp desktop='^8' state=floating follow=on
bspc rule -a Chromium desktop='^2'
bspc rule -a mplayer2 state=floating
bspc rule -a Kupfer.py focus=on
bspc rule -a Screenkey manage=off
```

Agregar los siguientes atajos al archivo `$HOME/.config/sxhkd/sxhkdrc` para abrir `firefox`

```bash
nano $HOME/.config/sxhkd/sxhkdrc
```

```bash
# open firefox
super + shift + f
  /usr/bin/firefox

# copy target
super + shift + alt + t
  $HOME/.config/bspwm/scripts/copy_target.sh
```

## Instalar fuente

```bash
sudo pacman -S ttf-jetbrains-mono
```

## Instalar kitty

```bash
sudo pacman -S kitty
```

Modificar las siguientes líneas al archivo `$HOME/.config/sxhkd/sxhkdrc`

```bash
nano $HOME/.config/sxhkd/sxhkdrc
```

```bash
# terminal emulator
super + Return
	/usr/bin/kitty
```

Crear el archivo `$HOME/.config/kitty/kitty.conf` y agregarle el siguiente contenido

```bash
mkdir -p ~/.config/kitty
nano ~/.config/kitty/kitty.conf
```

```bash
font_family HackNerdFont
cursor_shape beam

active_border_color #39ff14
inactive_border_color #5d5d5d

window_margin_width 2
window_border_width 1
window_padding_width 5

map ctrl+left neighboring_window left
map ctrl+right neighboring_window right
map ctrl+up neighboring_window up
map ctrl+down neighboring_window down

map ctrl+shift+enter new_window_with_cwd
map ctrl+shift+t new_tab_with_cwd

map f1 copy_to_buffer a
map f2 paste_from_buffer a
map f3 copy_to_buffer b
map f4 paste_from_buffer b
map f5 copy_to_buffer c
map f6 paste_from_buffer c
map f7 copy_to_buffer d
map f8 paste_from_buffer d
map f9 copy_to_buffer e
map f10 paste_from_buffer e

map ctrl+shift+z toggle_layout stack

tab_bar_style powerline

inactive_tab_background #e06c75
active_tab_background #98c379
inactive_tab_foreground #000000
tab_bar_margin_color #000

background_opacity 0.80
enable_audio_bell no
```

```bash
sudo mkdir -p /root/.config/kitty
sudo ln -s ~/.config/kitty/kitty.conf /root/.config/kitty/kitty.conf
```

## Instalar zsh y los plugins para que sugiera comandos ya ejecutados y el resaltado de comandos

```bash
sudo pacman -S zsh extra/zsh-autosuggestions extra/zsh-syntax-highlighting
```

```bash
sudo usermod --shell /usr/bin/zsh <user>
```

Agregar las siguientes líneas al archivo `$HOME/.zshrc` para que funcionen los plugins

```bash
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
source /home/arch/powerlevel10k/powerlevel10k.zsh-theme
```

Agregar la siguiente línea al archivo `$HOME/.zprofile`

````bash
nano $HOME/.zprofile
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && exec startx
```

### ESTO NO ESTA INSTALADO AUN ###

- `pacman -S p7zip mdcat firefox xclip`
- `paru -S scrub`
