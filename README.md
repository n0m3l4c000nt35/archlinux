![Arch Linux Artwork](https://archlinux.org/static/logos/archlinux-logo-light-90dpi.d36c53534a2b.png)

## Instalación

- [bspwmrc](#bspwmrc)
- [sxhkdrc](#sxhkdrc)
- [kitty](#kitty)
- [zsh](#zsh)
- [polybar](#polybar)

## bspwmrc

```bash
#! /bin/sh

pgrep -x sxhkd > /dev/null || sxhkd &

bspc monitor DP-0 -d I II III IV V
bspc monitor HDMI-0 -d VI VII VIII IX X

bspc desktop -f ^1

bspc config border_width         0
bspc config window_gap          12

bspc config split_ratio          0.52
bspc config borderless_monocle   true
bspc config gapless_monocle      true

feh --bg-center $HOME/wallpaper.ext

$HOME/.config/polybar/launch.sh &
```

## bspwm_resize

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

## sxhkdrc

```bash
mkdir -p $HOME/.config/sxhkd/
nano $HOME/.config/sxhkd/sxhkdrc
```

```bash
# terminal emulator
super + Return
	/usr/bin/kitty

# program launcher
super + @space
	dmenu_run

# make sxhkd reload its configuration files:
super + Escape
	pkill -USR1 -x sxhkd

# quit/restart bspwm
super + alt + {q,r}
	bspc {quit,wm -r}

# close and kill
super + {_,shift + }w
	bspc node -{c,k}

# alternate between the tiled and monocle layout
super + m
	bspc desktop -l next

# send the newest marked node to the newest preselected node
super + y
	bspc node newest.marked.local -n newest.!automatic.local

# swap the current node and the biggest window
super + g
	bspc node -s biggest.window

# set the window state
super + {t,shift + t,s,f}
	bspc node -t {tiled,pseudo_tiled,floating,fullscreen}

# set the node flags
super + ctrl + {m,x,y,z}
	bspc node -g {marked,locked,sticky,private}

# focus the node in the given direction
super + {_,shift + }{Left,Down,Up,Right}
	bspc node -{f,s} {west,south,north,east}

# focus the node for the given path jump
super + {p,b,comma,period}
	bspc node -f @{parent,brother,first,second}

# focus the next/previous window in the current desktop
super + {_,shift + }c
	bspc node -f {next,prev}.local.!hidden.window

# focus the next/previous desktop in the current monitor
super + bracket{left,right}
	bspc desktop -f {prev,next}.local

# focus the last node/desktop
super + {grave,Tab}
	bspc {node,desktop} -f last

# focus the older or newer node in the focus history
super + {o,i}
	bspc wm -h off; \
	bspc node {older,newer} -f; \
	bspc wm -h on

# focus or send to the given desktop
super + {_,shift + }{1-9,0}
	bspc {desktop -f,node -d} '^{1-9,10}'

# preselect the direction
super + ctrl + {Left,Down,Up,Right}
	bspc node -p {west,south,north,east}

# preselect the ratio
super + ctrl + {1-9}
	bspc node -o 0.{1-9}

# cancel the preselection for the focused node
super + ctrl + space
	bspc node -p cancel

# cancel the preselection for the focused desktop
super + ctrl + shift + space
	bspc query -N -d | xargs -I id -n 1 bspc node id -p cancel

# move a floating window
super + {Left,Down,Up,Right}
	bspc node -v {-20 0,0 20,0 -20,20 0}

# firefox
super + shift + f
	/usr/bin/firefox 2>/dev/null & disown

# copy target
super + shift + alt + t
  $HOME/.config/polybar/scripts/copy_target.sh

# flameshot
super + shift + alt + s
  /usr/bin/flameshot gui

# subir volumen
XF86AudioRaiseVolume
    pactl set-sink-volume @DEFAULT_SINK@ +1%

# bajar volumen
XF86AudioLowerVolume
    pactl set-sink-volume @DEFAULT_SINK@ -1%

# mutear/desmutear
XF86AudioMute
    pactl set-sink-mute @DEFAULT_SINK@ toggle

# burpsuite
super + shift + alt + b
  /usr/bin/burpsuite 2>/dev/null & disown

# chromium
super + shift g
  /usr/bin/chromium 2>/dev/null & disown

# bloodhound
super + shift + b
  /usr/bin/bloodhound 2>/dev/null & disown
```

## kitty

```bash
sudo pacman -S extra/kitty
```

```bash
mkdir -p $HOME/.config/kitty/
nano $HOME/.config/kitty/kitty.conf
```

```bash
cursor_shape beam
font_family monospace
font_size 13.0

map ctrl+shift+enter new_window_with_cwd
map ctrl+shift+t new_tab_with_cwd

map ctrl+left neighboring_window left
map ctrl+right neighboring_window right
map ctrl+up neighboring_window up
map ctrl+down neighboring_window down
map ctrl+shift+enter new_window_with_cwd
map ctrl+shift+t new_tab_with_cwd
map ctrl+shift+z toggle_layout stack

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

shell zsh
```

## zsh

```bash
sudo pacman -S zsh zsh-autosuggestions zsh-syntax-highlighting
```

```bash
nano $HOME/.zshrc
```

```bash
# Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
# Initialization code that may require console input (password prompts, [y/n]
# confirmations, etc.) must go above this block; everything else may go below.
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

if [[ -f /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh ]]; then
	source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
fi

if [[ -f /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh ]]; then
	source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
fi

if [[ -f /usr/share/zsh/plugins/zsh-sudo/sudo.plugin.zsh ]]; then
	source /usr/share/zsh/plugins/zsh-sudo/sudo.plugin.zsh
fi

# Use emacs key bindings
bindkey -e

# [Up-Arrow] - Up a line of history
if [[ -n "${terminfo[kcuu1]}" ]]; then
  bindkey -M emacs "${terminfo[kcuu1]}" up-line-or-history
  bindkey -M viins "${terminfo[kcuu1]}" up-line-or-history
  bindkey -M vicmd "${terminfo[kcuu1]}" up-line-or-history
fi

# [Down-Arrow] - Down a line of history
if [[ -n "${terminfo[kcud1]}" ]]; then
  bindkey -M emacs "${terminfo[kcud1]}" down-line-or-history
  bindkey -M viins "${terminfo[kcud1]}" down-line-or-history
  bindkey -M vicmd "${terminfo[kcud1]}" down-line-or-history
fi

if [[ -n "${terminfo[khome]}" ]]; then
  bindkey -M emacs "${terminfo[khome]}" beginning-of-line
  bindkey -M viins "${terminfo[khome]}" beginning-of-line
  bindkey -M vicmd "${terminfo[khome]}" beginning-of-line
  bindkey "^[[H" beginning-of-line
fi

# [End] - Go to end of line
if [[ -n "${terminfo[kend]}" ]]; then
  bindkey -M emacs "${terminfo[kend]}"  end-of-line
  bindkey -M viins "${terminfo[kend]}"  end-of-line
  bindkey -M vicmd "${terminfo[kend]}"  end-of-line
  bindkey "^[[F" end-of-line
fi

# [Shift-Tab] - move through the completion menu backwards
if [[ -n "${terminfo[kcbt]}" ]]; then
  bindkey -M emacs "${terminfo[kcbt]}" reverse-menu-complete
  bindkey -M viins "${terminfo[kcbt]}" reverse-menu-complete
  bindkey -M vicmd "${terminfo[kcbt]}" reverse-menu-complete
fi

# [Backspace] - delete backward
bindkey -M emacs '^?' backward-delete-char
bindkey -M viins '^?' backward-delete-char
bindkey -M vicmd '^?' backward-delete-char

# [Delete] - delete forward
if [[ -n "${terminfo[kdch1]}" ]]; then
  bindkey -M emacs "${terminfo[kdch1]}" delete-char
  bindkey -M viins "${terminfo[kdch1]}" delete-char
  bindkey -M vicmd "${terminfo[kdch1]}" delete-char
else
  bindkey -M emacs "^[[3~" delete-char
  bindkey -M viins "^[[3~" delete-char
  bindkey -M vicmd "^[[3~" delete-char

  bindkey -M emacs "^[3;5~" delete-char
  bindkey -M viins "^[3;5~" delete-char
  bindkey -M vicmd "^[3;5~" delete-char
fi

# [Ctrl-Delete] - delete whole forward-word
bindkey -M emacs '^[[3;5~' kill-word
bindkey -M viins '^[[3;5~' kill-word
bindkey -M vicmd '^[[3;5~' kill-word

# [Ctrl-RightArrow] - move forward one word
bindkey -M emacs '^[[1;5C' forward-word
bindkey -M viins '^[[1;5C' forward-word
bindkey -M vicmd '^[[1;5C' forward-word

# [Ctrl-LeftArrow] - move backward one word
bindkey -M emacs '^[[1;5D' backward-word
bindkey -M viins '^[[1;5D' backward-word
bindkey -M vicmd '^[[1;5D' backward-word

alias cat='bat'
alias catn='bat --style=plain'
alias catnp='bat --style=plain --paging=never'
alias ll='lsd -lh --group-dirs=first'
alias la='lsd -a --group-dirs=first'
alias l='lsd --group-dirs=first'
alias lla='lsd -lha --group-dirs=first'
alias ls='lsd --group-dirs=first'
alias firefox='firefox 2>/dev/null & disown'

source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh
source <(fzf --zsh)
```

## polybar

```bash
nano $HOME/.config/polybar/config.ini
```

```bash
[bar/main]
width = 99.5%
height = 40
offset-x = 0.25%
offset-y = 1%
module-margin = 7pt
padding-left = 2
padding-right = 2
background = #aa000000
modules-left = date ethernet vpn
modules-center = workspaces
modules-right = target pulseaudio
font-0 = "Hack Nerd Font Mono:style=regular:size=10;1"
font-1 = "Hack Nerd Font Mono:style=regular:size=16;2"
font-2 = "Hack Nerd Font Mono:style=regular:size=18;2"
font-3 = "Hack Nerd Font Mono:style=regular:size=20;4"

[module/date]
type = internal/date
interval = 1.0
date = %d/%m/%Y%
time = %H:%M:%S
format = <label>
format-foreground = #fff
label = %date% %{T2}%{F#ff1493}%{F-}%{T-} %time%

[module/ethernet]
type = custom/script
exec = $HOME/.config/polybar/scripts/ethernet_status.sh
interval = 2
format = <label>

[module/vpn]
type = custom/script
exec = $HOME/.config/polybar/scripts/vpn_status.sh
click-left = echo -n "$(ip a show tun0 | grep -oP '(?<=inet\s)\d+\.\d+\.\d+\.\d+')" | xclip -sel clip
interval = 2
format = <label>

[module/workspaces]
type = internal/xworkspaces
icon-default = 
format = <label-state>
format-font = 3
label-active = 󱓇
label-active-foreground = #39ff14
label-active-padding = 5px
label-active-font = 4
label-occupied = %icon%
label-occupied-foreground = #4439ff14
label-occupied-padding = 5px
label-occupied-font = 2
label-urgent = %icon%
label-urgent-foreground = #e51d0b
label-urgent-padding = 5px
label-empty = %icon%
label-empty-foreground = #6a6a6a
label-empty-padding = 5px
label-empty-font = 2

[module/target]
type = custom/script
exec = $HOME/.config/polybar/scripts/target_to_hack.sh
click-left = echo -n "$(cat $HOME/.config/polybar/scripts/target.txt)" | xclip -sel clip
interval = 2
format = <label>

[module/pulseaudio]
type = internal/pulseaudio
use-ui-max = true
interval = 1
reverse-scroll = false
format-volume = <label-volume>
label-muted = muted
label-muted-foreground = #666
click-right = pavucontrol
```

```bash
nano $HOME/.config/polybar/launch.sh
```

```bash
#!/bin/bash

killall -q polybar

polybar main -c $HOME/.config/polybar/config.ini
```
