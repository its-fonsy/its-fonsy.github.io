# Archlinux packages

## Extra packages to install from official repository

- arandr
- atool
- autorandr
- beets
- bitwarden
- blueman
- bluez
- bluez-utils
- cmus
- cronie
- discord
- dunst
- feh
- firefox
- flameshot
- freecad
- gimp
- git
- inkscape
- libreoffice-still
- mpv
- neovim
- noto-fonts-emoji
- prusa-slicer
- pulsemixer
- ranger
- ripgrep
- rsync
- thunar
- tmux
- ttc-iosevka
- ttf-ubuntu-font-family
- unzip
- w3m
- xclip
- xdg-user-dirs
- xf86-input-synaptics
- zathura
- zoxide
- zsh
- zsh-completions

## Extra packages to install from AUR

First install `yay`

    cd /tmp
    sudo pacman -S --needed git base-devel
    git clone https://aur.archlinux.org/yay.git
    cd yay
    makepkg -si 

Then install

    yay -S megasync-bin

## Services to enable & start

    sudo systemctl enable {bluetooth,cronie}
    sudo systemctl start {bluetooth,cronie}

## Extra commands to init

Set XDG user direcotries

    xdg-user-dirs-update

Change shell to zsh

    chsh /usr/bin/zsh

Set keyboard layout eurkey

    localectl set-x11-keymap eu

Apply dotfiles

    cd $HOME
    git clone https://github.com/its-fonsy/dotfiles.git .dots
    cd .dots
    make init

