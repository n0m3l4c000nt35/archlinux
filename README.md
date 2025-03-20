![Arch Linux Artwork](https://archlinux.org/static/logos/archlinux-logo-light-90dpi.d36c53534a2b.png)

## Instalación

1. [Configuracion bspwm](#configuracion-bspwm)

## Configuracion bspwm

```bash
#!/bin/sh

pgrep -x sxhkd > /dev/null || sxhkd &

bspc monitor -d I II III IV V VI VII VIII IX X

bspc config border_width         0
bspc config window_gap           12
bspc config split_ratio          0.5
bspc config borderless_monocle   true
bspc config gapless_monocle      true

$HOME/.config/polybar/launch.sh &
```
