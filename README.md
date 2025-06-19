![Arch Linux Artwork](https://archlinux.org/static/logos/archlinux-logo-light-90dpi.d36c53534a2b.png)

- [Actualización](#actualizacion)
- [Instalación de paquetes](#instalacion-de-paquetes)
- [Permisos](#permisos)
- [.xinitrc](#xinitrc)
- [.Xresources](#xresources)
- [Fuentes](#fuentes)
- [bspwm](#bspwm)
  - [bspwmrc](#bspwmrc)
  - [bspwm_resize](#bspwm_resize)
  - [sxhkdrc](#sxhkdrc)
- [picom](#picom)
  - [picom.conf](#picomconf)
- [kitty](#kitty)
  - [kitty.conf](/files/kitty.conf)
- [rofi](#rofi)
  - [config.rasi](/files/configrasi)
  - [box-theme.rasi](/files/box-themerasi)

## Actualizacion

```bash
sudo pacman -Syu
```

## Instalacion de paquetes

```bash
sudo pacman -S xorg dkms bspwm kitty feh polybar picom rofi i3lock flameshot xclip firefox-esr locate unzip openvpn
```

## Creación de directorios

```bash
mkdir -p $HOME/.config/{bspwm,sxhkd,picom,kitty,polybar,rofi,htb}
mkdir $HOME/.config/bspwm/scripts
mkdir $HOME/.config/polybar/scripts
mkdir $HOME/.config/htb/{vpn,machines}
mkdir $HOME/htb/machines
sudo mkdir /opt/nvim
sudo mkdir /usr/share/fonts/truetype/hacknerd
sudo mkdir /usr/share/zsh-sudo
```

## Permisos

```bash
chmod +x $HOME/.xinitrc
chmod u+x $HOME/.config/bspwm/bspwmrc
chmod +x $HOME/.config/polybar/scripts/{ethernet_status.sh,vpn_status.sh,target_to_hack.sh,copy_target.sh}
chmod +x $HOME/.config/bspwm/scripts/bspwm_resize
chmod +x $HOME/.config/polybar/launch.sh
```

## .xinitrc

[.xinitrc](/files/.xinitrc)

## .Xresources

[.Xresources](/files/.Xresources)

## Fuentes

```bash
sudo wget -P /usr/share/fonts/truetype/hacknerd https://github.com/ryanoasis/nerd-fonts/releases/download/v3.4.0/Hack.zip
sudo unzip /usr/share/fonts/truetype/hacknerd/Hack.zip -d /usr/share/fonts/truetype/hacknerd
sudo rm /usr/share/fonts/truetype/hacknerd/LICENSE.md /usr/share/fonts/truetype/hacknerd/README.md /usr/share/fonts/truetype/hacknerd/Hack.zip
```

## bspwm

### bspwmrc

[bspwmrc](/files/bspwmrc)

### bspwm_resize

[bspwm_resize](/files/bspwm_resize)

### sxhkdrc

[sxhkdrc](/files/sxhkdrc)

## picom

### picom.conf

[picom.conf](/files/picom.conf)

## kitty

Seleccionar el theme de preferencia y seleccionar la opción Place the theme file in /home/kali/.config/kitty but do not modify kitty.conf. En mi caso seleccioné el theme Box y agregué la línea include Box.conf al archivo kitty.conf como se muestra a continuación.

```bash
kitten themes
```

### kitty.conf

[kitty.conf](/files/kitty.conf)

## rofi

### config.rasi

[config.rasi](/files/config.rasi)

### box-theme.rasi

[box-theme.rasi](/files/box-theme.rasi)
